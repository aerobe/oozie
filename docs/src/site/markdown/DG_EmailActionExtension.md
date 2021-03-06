

[::Go back to Oozie Documentation Index::](index.html)

-----

# Oozie Email Action Extension

<!-- MACRO{toc|fromDepth=1|toDepth=4} -->

<a name="EmailAction"></a>
## 3.2.4 Email action

The `email` action allows sending emails in Oozie from a workflow application. An email action must provide `to`
addresses, `cc` addresses (optional), `bcc` addresses (optional), a `subject` and a `body`.
Multiple recipients of an email can be provided as comma separated addresses.

The email action is executed synchronously, and the workflow job will wait until the specified
emails are sent before continuing to the next action.

All values specified in the `email` action can be parameterized (templatized) using EL expressions.

**Syntax:**


```
<workflow-app name="[WF-DEF-NAME]" xmlns="uri:oozie:workflow:0.1">
    ...
    <action name="[NODE-NAME]">
        <email xmlns="uri:oozie:email-action:0.2">
            <to>[COMMA-SEPARATED-TO-ADDRESSES]</to>
            <cc>[COMMA-SEPARATED-CC-ADDRESSES]</cc> <!-- cc is optional -->
            <bcc>[COMMA-SEPARATED-BCC-ADDRESSES]</bcc> <!-- bcc is optional -->
            <subject>[SUBJECT]</subject>
            <body>[BODY]</body>
            <content_type>[CONTENT-TYPE]</content_type> <!-- content_type is optional -->
            <attachment>[COMMA-SEPARATED-HDFS-FILE-PATHS]</attachment> <!-- attachment is optional -->
        </email>
        <ok to="[NODE-NAME]"/>
        <error to="[NODE-NAME]"/>
    </action>
    ...
</workflow-app>
```

The `to` and `cc` and `bcc` commands are used to specify recipients who should get the mail. Multiple email recipients
can be provided using comma-separated values. Providing a `to` command is necessary, while the `cc` or `bcc` may
optionally be used along.

The `subject` and `body` commands are used to specify subject and body of the mail.
From uri:oozie:email-action:0.2 one can also specify mail content type as <content_type>text/html</content_type>.
"text/plain" is default.

The `attachment` is used to attach a file(s) on HDFS to the mail. Multiple attachment can be provided using comma-separated values.
Non fully qualified path is considered as a file on default HDFS. A local file cannot be attached.

**Configuration**

The `email` action requires some SMTP server configuration to be present (in oozie-site.xml). The following are the values
it looks for:

   * `oozie.email.smtp.host` - The host where the email action may find the SMTP server (localhost by default).
   * `oozie.email.smtp.port` - The port to connect to for the SMTP server (25 by default).
   * `oozie.email.from.address` - The from address to be used for mailing all emails (oozie@localhost by default).
   * `oozie.email.smtp.auth` - Boolean property that toggles if authentication is to be done or not. (false by default).
   * `oozie.email.smtp.starttls.enable` - Boolean property that toggles if use TLS communication or not. (false by default).
   * `oozie.email.smtp.username` - If authentication is enabled, the username to login as (empty by default).
   * `oozie.email.smtp.password` - If authentication is enabled, the username's password (empty by default).
   * `oozie.email.attachment.enabled` - Boolean property that toggles if configured attachments are to be placed into the emails.
   (false by default).
   * `oozie.email.smtp.socket.timeout.ms` - The timeout to apply over all SMTP server socket operations (10000ms by default).

**Example:**


```
<workflow-app name="sample-wf" xmlns="uri:oozie:workflow:0.1">
    ...
    <action name="an-email">
        <email xmlns="uri:oozie:email-action:0.1">
            <to>bob@initech.com,the.other.bob@initech.com</to>
            <cc>will@initech.com</cc>
            <bcc>yet.another.bob@initech.com</bcc>
            <subject>Email notifications for ${wf:id()}</subject>
            <body>The wf ${wf:id()} successfully completed.</body>
        </email>
        <ok to="myotherjob"/>
        <error to="errorcleanup"/>
    </action>
    ...
</workflow-app>
```

In the above example, an email is sent to 'bob', 'the.other.bob', 'will' (cc), yet.another.bob (bcc)
with the subject and body both containing the workflow ID after substitution.

## AE.A Appendix A, Email XML-Schema


```
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           xmlns:email="uri:oozie:email-action:0.2" elementFormDefault="qualified"
           targetNamespace="uri:oozie:email-action:0.2">
.
    <xs:element name="email" type="email:ACTION"/>
.
    <xs:complexType name="ACTION">
        <xs:sequence>
            <xs:element name="to" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="cc" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="bcc" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="subject" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="body" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="content_type" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="attachment" type="xs:string" minOccurs="0" maxOccurs="1"/>
        </xs:sequence>
    </xs:complexType>
</xs:schema>
```

**GMail example to oozie-site.xml**


```
oozie.email.smtp.host=smtp.gmail.com
oozie.email.smtp.port=587
oozie.email.from.address=<some email address>
oozie.email.smtp.auth=true
oozie.email.smtp.starttls.enable=true
oozie.email.smtp.username=<Gmail Id>
oozie.email.smtp.password=<Gmail Pass>
```

[::Go back to Oozie Documentation Index::](index.html)



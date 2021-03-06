

[::Go back to Oozie Documentation Index::](index.html)

-----

# Oozie Hive 2 Action Extension

<!-- MACRO{toc|fromDepth=1|toDepth=4} -->

## Hive 2 Action

The `hive2` action runs Beeline to connect to Hive Server 2.

The workflow job will wait until the Hive Server 2 job completes before
continuing to the next action.

To run the Hive Server 2 job, you have to configure the `hive2` action with the `resource-manager`, `name-node`, `jdbc-url`,
 `password` elements, and either Hive's `script` or `query` element, as well as the necessary parameters and configuration.

A `hive2` action can be configured to create or delete HDFS directories
before starting the Hive Server 2 job.

Oozie EL expressions can be used in the inline configuration. Property
values specified in the `configuration` element override values specified
in the `job-xml` file.

As with Hadoop `map-reduce` jobs, it is possible to add files and
archives in order to make them available to Beeline. Refer to the
[Adding Files and Archives for the Job](WorkflowFunctionalSpec.html#FilesArchives)
section for more information about this feature.

Oozie Hive 2 action supports Hive scripts with parameter variables, their
syntax is `${VARIABLES}`.

**Syntax:**


```
<workflow-app name="[WF-DEF-NAME]" xmlns="uri:oozie:workflow:1.0">
    ...
    <action name="[NODE-NAME]">
        <hive2 xmlns="uri:oozie:hive2-action:1.0">
            <resource-manager>[RESOURCE-MANAGER]</resource-manager>
            <name-node>[NAME-NODE]</name-node>
            <prepare>
               <delete path="[PATH]"/>
               ...
               <mkdir path="[PATH]"/>
               ...
            </prepare>
            <job-xml>[HIVE SETTINGS FILE]</job-xml>
            <configuration>
                <property>
                    <name>[PROPERTY-NAME]</name>
                    <value>[PROPERTY-VALUE]</value>
                </property>
                ...
            </configuration>
            <jdbc-url>[jdbc:hive2://HOST:10000/default]</jdbc-url>
            <password>[PASS]</password>
            <script>[HIVE-SCRIPT]</script>
            <param>[PARAM-VALUE]</param>
                ...
            <param>[PARAM-VALUE]</param>
            <argument>[ARG-VALUE]</argument>
                ...
            <argument>[ARG-VALUE]</argument>
            <file>[FILE-PATH]</file>
            ...
            <archive>[FILE-PATH]</archive>
            ...
        </hive2>
        <ok to="[NODE-NAME]"/>
        <error to="[NODE-NAME]"/>
    </action>
    ...
</workflow-app>
```

The `prepare` element, if present, indicates a list of paths to delete
or create before starting the job. Specified paths must start with `hdfs://HOST:PORT`.

The `job-xml` element, if present, specifies a file containing configuration
for Beeline. Multiple `job-xml` elements are allowed in order to specify multiple `job.xml` files.

The `configuration` element, if present, contains configuration
properties that are passed to the Beeline job.

The `jdbc-url` element must contain the JDBC URL for the Hive Server 2.  Beeline will use this to know where to connect to.

The `password` element must contain the password of the current user.  However, the `password` is only used if Hive Server 2 is
backed by something requiring a password (e.g. LDAP); non-secured Hive Server 2 or Kerberized Hive Server 2 don't require a password
so in those cases the `password` is ignored and can be omitted from the action XML.  It is up to the user to ensure that a password
is specified when required.

The `script` element must contain the path of the Hive script to
execute. The Hive script can be templatized with variables of the form
`${VARIABLE}`. The values of these variables can then be specified
using the `params` element.

The `query` element available from uri:oozie:hive2-action:0.2, can be used instead of the `script` element. It allows for embedding
queries within the `worklfow.xml` directly.  Similar to the `script` element, it also allows for the templatization of variables
in the form `${VARIABLE}`.

The `params` element, if present, contains parameters to be passed to
the Hive script.

The `argument` element, if present, contains arguments to be passed as-is to Beeline.

All the above elements can be parameterized (templatized) using EL
expressions.

**Example:**


```
<workflow-app name="sample-wf" xmlns="uri:oozie:workflow:1.0">
    ...
    <action name="my-hive2-action">
        <hive2 xmlns="uri:oozie:hive2-action:1.0">
            <resource-manager>foo:8032</resource-manager>
            <name-node>bar:8020</name-node>
            <prepare>
                <delete path="${jobOutput}"/>
            </prepare>
            <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
            </configuration>
            <jdbc-url>jdbc:hive2://localhost:10000/default</jdbc-url>
            <password>foo</password>
            <script>myscript.q</script>
            <param>InputDir=/home/rkanter/input-data</param>
            <param>OutputDir=${jobOutput}</param>
        </hive2>
        <ok to="my-other-action"/>
        <error to="error-cleanup"/>
    </action>
    ...
</workflow-app>
```


### Security

As mentioned above, `password` is only used in cases where Hive Server 2 is backed by something requiring a password (e.g. LDAP).
Non-secured Hive Server 2 and Kerberized Hive Server 2 don't require a password so in these cases it can be omitted.

## Appendix, Hive 2 XML-Schema

### AE.A Appendix A, Hive 2 XML-Schema

#### Hive 2 Action Schema Version 1.0

```
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           xmlns:hive2="uri:oozie:hive2-action:1.0" elementFormDefault="qualified"
           targetNamespace="uri:oozie:hive2-action:1.0">
.
    <xs:include schemaLocation="oozie-common-1.0.xsd"/>
.
    <xs:element name="hive2" type="hive2:ACTION"/>
.
    <xs:complexType name="ACTION">
        <xs:sequence>
            <xs:choice>
                <xs:element name="job-tracker" type="xs:string" minOccurs="0" maxOccurs="1"/>
                <xs:element name="resource-manager" type="xs:string" minOccurs="0" maxOccurs="1"/>
            </xs:choice>
            <xs:element name="name-node" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="prepare" type="hive2:PREPARE" minOccurs="0" maxOccurs="1"/>
            <xs:element name="launcher" type="hive2:LAUNCHER" minOccurs="0" maxOccurs="1"/>
            <xs:element name="job-xml" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="configuration" type="hive2:CONFIGURATION" minOccurs="0" maxOccurs="1"/>
            <xs:element name="jdbc-url" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="password" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:choice minOccurs="1" maxOccurs="1">
                <xs:element name="script" type="xs:string" minOccurs="1" maxOccurs="1"/>
                <xs:element name="query" type="xs:string" minOccurs="1" maxOccurs="1"/>
            </xs:choice>
            <xs:element name="param" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="argument" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="file" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="archive" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
    </xs:complexType>
.
</xs:schema>
```

#### Hive 2 Action Schema Version 0.2

```
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           xmlns:hive2="uri:oozie:hive2-action:0.2" elementFormDefault="qualified"
           targetNamespace="uri:oozie:hive2-action:0.2">
.
    <xs:element name="hive2" type="hive2:ACTION"/>
.
    <xs:complexType name="ACTION">
        <xs:sequence>
            <xs:element name="job-tracker" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="name-node" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="prepare" type="hive2:PREPARE" minOccurs="0" maxOccurs="1"/>
            <xs:element name="job-xml" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="configuration" type="hive2:CONFIGURATION" minOccurs="0" maxOccurs="1"/>
            <xs:element name="jdbc-url" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="password" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:choice minOccurs="1" maxOccurs="1">
                <xs:element name="script" type="xs:string" minOccurs="1" maxOccurs="1"/>
                <xs:element name="query"  type="xs:string" minOccurs="1" maxOccurs="1"/>
            </xs:choice>
            <xs:element name="param" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="argument" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="file" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="archive" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
    </xs:complexType>
.
    <xs:complexType name="CONFIGURATION">
        <xs:sequence>
            <xs:element name="property" minOccurs="1" maxOccurs="unbounded">
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="name" minOccurs="1" maxOccurs="1" type="xs:string"/>
                        <xs:element name="value" minOccurs="1" maxOccurs="1" type="xs:string"/>
                        <xs:element name="description" minOccurs="0" maxOccurs="1" type="xs:string"/>
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
.
    <xs:complexType name="PREPARE">
        <xs:sequence>
            <xs:element name="delete" type="hive2:DELETE" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="mkdir" type="hive2:MKDIR" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
    </xs:complexType>
.
    <xs:complexType name="DELETE">
        <xs:attribute name="path" type="xs:string" use="required"/>
    </xs:complexType>
.
    <xs:complexType name="MKDIR">
        <xs:attribute name="path" type="xs:string" use="required"/>
    </xs:complexType>
.
</xs:schema>
```

#### Hive 2 Action Schema Version 0.1

```
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           xmlns:hive2="uri:oozie:hive2-action:0.1" elementFormDefault="qualified"
           targetNamespace="uri:oozie:hive2-action:0.1">
.
    <xs:element name="hive2" type="hive2:ACTION"/>
.
    <xs:complexType name="ACTION">
        <xs:sequence>
            <xs:element name="job-tracker" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="name-node" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="prepare" type="hive2:PREPARE" minOccurs="0" maxOccurs="1"/>
            <xs:element name="job-xml" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="configuration" type="hive2:CONFIGURATION" minOccurs="0" maxOccurs="1"/>
            <xs:element name="jdbc-url" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="password" type="xs:string" minOccurs="0" maxOccurs="1"/>
            <xs:element name="script" type="xs:string" minOccurs="1" maxOccurs="1"/>
            <xs:element name="param" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="argument" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="file" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="archive" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
    </xs:complexType>
.
    <xs:complexType name="CONFIGURATION">
        <xs:sequence>
            <xs:element name="property" minOccurs="1" maxOccurs="unbounded">
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="name" minOccurs="1" maxOccurs="1" type="xs:string"/>
                        <xs:element name="value" minOccurs="1" maxOccurs="1" type="xs:string"/>
                        <xs:element name="description" minOccurs="0" maxOccurs="1" type="xs:string"/>
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
.
    <xs:complexType name="PREPARE">
        <xs:sequence>
            <xs:element name="delete" type="hive2:DELETE" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element name="mkdir" type="hive2:MKDIR" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
    </xs:complexType>
.
    <xs:complexType name="DELETE">
        <xs:attribute name="path" type="xs:string" use="required"/>
    </xs:complexType>
.
    <xs:complexType name="MKDIR">
        <xs:attribute name="path" type="xs:string" use="required"/>
    </xs:complexType>
.
</xs:schema>
```

[::Go back to Oozie Documentation Index::](index.html)



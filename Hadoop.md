# Hadoop y HDFS

Instalar: [https://tecadmin.net/setup-hadoop-on-ubuntu/]

o en el archivo: [HadoopHdfsInstall.pdf](../HadoopHdfsInstall.pdf)

# HBase

Instalar: [https://www.bogotobogo.com/Hadoop/BigData_hadoop_HBase_Pseudo_Distributed.php]

o en el archivo: HBaseInstall.pdf

Adicionalmente es necesario, al correr en local filesystem, para que no se cuelgue el HMaster, incluir en: hbase-site.xml:

```
<property>
    <name>hbase.unsafe.stream.capability.enforce</name>
    <value>false</value>
    <description>
        Controls whether HBase will check for stream capabilities (hflush/hsync).
        Disable this if you intend to run on LocalFileSystem, denoted by a rootdir
        with the 'file://' scheme, but be mindful of the NOTE below.
        WARNING: Setting this to false blinds you to potential data loss and
        inconsistent system state in the event of process and/or node failures. If
        HBase is complaining of an inability to use hsync or hflush it's most
        likely not a false positive.
    </description>
</property>
```
# OpenTSDB

Instalar: [http://opentsdb.net/docs/build/html/installation.html]

o en el archivo: OpenTsdbInstall.pdf

Con el paquete .deb funcionó bien: [https://github.com/OpenTSDB/opentsdb/releases/download/v2.4.0/opentsdb-2.4.0_all.deb]

Para la creación inicial de tablas la instrucción exacta fue:
`cd /usr/share/opentsdb/tools`
`env COMPRESSION=NONE HBASE_HOME=/mnt/Secundario/hbase ./create_table.sh`

Información adicional de conffiguración en: [http://opentsdb.net/docs/build/html/user_guide/configuration.html]

Arrancar con: `service opentsdb start`

Detener con: `service opentsdb stop`

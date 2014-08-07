jdbf
====

Java utility to read/write DBF files

### Version 2.0.1 

Fix issue [#3](https://github.com/iryndin/jdbf/issues/3) - read the last record two times for "FoxBASE+/Dbase III plus" files

Fix issue [#4](https://github.com/iryndin/jdbf/issues/4) - incorrect parsing of update date in DBF header for "FoxBASE+/Dbase III plus" files

### Version 2.0 

Add ability to read MEMO files (tested with Visual FoxPro DBFs)

[![Dependency Status](https://www.versioneye.com/user/projects/53c55ce7c4a986cbb3000002/badge.svg?style=flat)](https://www.versioneye.com/user/projects/53c55ce7c4a986cbb3000002)

## User Guide

### Read DBF file 

Piece of code that reads file from classpath. Single DBF record is represented here as a Map.

See [TestDbfReader.java](src/test/java/net/iryndin/jdbf/TestDbfReader.java)

```java
    public void readDBF() throws IOException, ParseException {
        Charset stringCharset = Charset.forName("Cp866");

        InputStream dbf = getClass().getClassLoader().getResourceAsStream("data1/gds_im.dbf");

        DbfRecord rec;
        try (DbfReader reader = new DbfReader(dbf)) {
            DbfMetadata meta = reader.getMetadata();

            System.out.println("Read DBF Metadata: " + meta);
            while ((rec = reader.read()) != null) {
                rec.setStringCharset(stringCharset);
                System.out.println("Record #" + rec.getRecordNumber() + ": " + rec.toMap());
            }
        }
    }
```

### Read DBF file with MEMO fields

Piece of code that reads DBF and MEMO fields. 

See [TestMemo.java](src/test/java/net/iryndin/jdbf/TestMemo.java)

```java
    public void test1() {
        Charset stringCharset = Charset.forName("cp1252");

        InputStream dbf = getClass().getClassLoader().getResourceAsStream("memo1/texto.dbf");
        InputStream memo = getClass().getClassLoader().getResourceAsStream("memo1/texto.fpt");

        try (DbfReader reader = new DbfReader(dbf, memo)) {
            DbfMetadata meta = reader.getMetadata();
            System.out.println("Read DBF Metadata: " + meta);

            DbfRecord rec;
            while ((rec = reader.read()) != null) {
                rec.setStringCharset(stringCharset);

                System.out.println("TEXVER: " + rec.getString("TEXVER"));
                // this reads MEMO field
                System.out.println("TEXTEX: " + rec.getMemoAsString("TEXTEX"));
                System.out.println("TEXDAT: " + rec.getDate("TEXDAT"));
                System.out.println("TEXSTA: " + rec.getString("TEXSTA"));
                System.out.println("TEXCAM: " + rec.getString("TEXCAM"));
                System.out.println("++++++++++++++++++++++++++++++++++");
            }

        } catch (IOException e) {
            //e.printStackTrace();
        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
```

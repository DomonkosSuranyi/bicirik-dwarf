![](http://www.meleklermekani.com/imagehosting/Aydin-Babaoglu-0-259.jpg)
# bicirik-dwarf
[![Build Status](https://travis-ci.org/aykutkilic/bicirik-dwarf.svg?branch=master)](https://travis-ci.org/aykutkilic/bicirik-dwarf)

Java based Elf/Dwarf parser branched from peter-dwarf. [https://code.google.com/p/peter-dwarf/]

How to use
==========
Now this project can be used via Maven.

build
-----
- git clone git@github.com:aykutkilic/bicirik-dwarf.git
- cd bicirik-dwarf/com.bicirikdwarf
- mvn install

adding to your project
----------------------
- navigate to your project folder
- add new dependency to your project's pom.xml
```
<dependency>
	<groupId>com.bicirikdwarf</groupId>
	<artifactId>bicirikdwarf</artifactId>
	<version>1.1.0</version>
</dependency>
```
(check the newest version number before)

example usage
-------------
This is a dummy example to parse the ELF/DWARF file and the address of the variable called "car".
```
package com.my.project.that.uses.bicirik;

import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.file.Path;
import java.nio.file.Paths;

import com.bicirikdwarf.dwarf.CompilationUnit;
import com.bicirikdwarf.dwarf.DebugInfoEntry;
import com.bicirikdwarf.dwarf.DwAtType;
import com.bicirikdwarf.dwarf.Dwarf32Context;
import com.bicirikdwarf.elf.Elf32Context;

public class MyClass {
    private static final Path OUT_FILE_PATH = Paths.get("path/to/my/elf/file/main.elf");
    
    //print the address of the variable called "car"
    public void main() throws IOException {
        ByteBuffer elfBuf = getBufferFromFile(OUT_FILE_PATH);
        Elf32Context elf = new Elf32Context(elfBuf);
        Dwarf32Context dwarf = new Dwarf32Context(elf);
	
	//Do what you want
        for(CompilationUnit iCU : dwarf.getCompilationUnits())
            for(DebugInfoEntry iDIE : iCU.getCompileUnit().getChildren())
                if("car".equals(iDIE.getAttribValue(DwAtType.DW_AT_name))) {
		    System.out.println("The address of variable car: " + iDIE.address);
		    return;
		}
    }
    
    //The steps in this function must be done before init Elf32Context
    private static ByteBuffer getBufferFromFile(Path filePath) throws IOException {
	try (RandomAccessFile randomAccessFile = new RandomAccessFile(filePath.toString(), "r")) {
	    FileChannel fileChannel = randomAccessFile.getChannel();
            long fileSize = fileChannel.size();
            ByteBuffer byteBuffer = ByteBuffer.allocate((int) fileSize);
            fileChannel.read(byteBuffer);
            randomAccessFile.close();
            byteBuffer.flip();
            return byteBuffer;
        }
    }
}
```

The parser has been tested with GCC 4.9.3. and GHS compilers.

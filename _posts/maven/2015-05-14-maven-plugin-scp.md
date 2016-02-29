---
layout: post
title: maven-antrun-plugin
category: build
tags: maven
keywords: 
description: 
---
```
<plugins>
    <plugin>
        <!--<groupId>org.apache.maven</groupId>-->
        <artifactId>maven-antrun-plugin</artifactId>
        <executions>
            <execution>
                <id>scp</id>
                <phase>package</phase>
                <goals>
                    <goal>run</goal>
                </goals>
                <configuration>
                    <tasks>
                        <echo message="Hello, world1"/>
                        <scp trust="true" todir="swa:woshishui@localhost:/Users/swa/uic/test">
                            <fileset dir="${project.basedir}/target" includes="classes/"/>
                        </scp>
                    </tasks>
                </configuration>
            </execution>
        </executions>
        <dependencies>
            <dependency>
                <groupId>org.apache.ant</groupId>
                <artifactId>ant-jsch</artifactId>
                <version>1.9.4</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>
```

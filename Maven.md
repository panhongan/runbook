**【Plugins】**  
**1. antrun**  
copy file/fileset/directory
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-antrun-plugin</artifactId>
    <version>1.7</version>
    <executions>
        <execution>
            <phase>install</phase>
            <configuration>
                <tasks>
                    <copy file="${project.build.directory}/${project.name}-${project.version}.jar"
                          tofile="${project.build.directory}/${project.name}-${project.version}-release/lib/${project.name}-${project.version}.jar"/>
                    <copydir src="${project.basedir}/bin" dest="${project.build.directory}/${project.name}-${project.version}-release/bin" />
                    <copydir src="${project.basedir}/conf" dest="${project.build.directory}/${project.name}-${project.version}-release/conf" />
                </tasks>
            </configuration>
            <goals>
                <goal>run</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

2. 
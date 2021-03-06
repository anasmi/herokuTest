# Deploying a Vaadin application to Heroku

This simple project is used to verify how deplyment of Vaadin application works with Heroku. 
Since spring-boot application is packaged by default as a jar, there are three different deployment possibilites:

- Deploy a pre-build in production mode jar
- Deploy a pre-build in production mode war
- Associate a github repo with an app on Heroku service

All three are possible with vaadin.

### Prerequisites 
- Java is installed and on Windows machine added to the PATH variable
- Git is installed locally (Can check by running `git --version`)
- A Heroku account is created

## Running a jar

1. Install Heroku CLI (https://devcenter.heroku.com/articles/heroku-cli)

2. Install Java CLI plugin (`heroku plugins:install java`)

3. Change the server.port in application.properties settings to `server.port=${port:8080}` (https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-change-the-location-of-external-properties)

4. Generate a jar from your application using `mvn package -Pproduction`

5. Navigate to the folder, where generated jar is

    <i>Next steps are following the section here: [Using the Heroku Java CLI Plugin](https://devcenter.heroku.com/articles/deploying-executable-jar-files#using-the-heroku-java-cli-plugin)</i>

6. Create a Heroku application by either running
      - `heroku create APP_NAME` in the CLI`
      - Or in the UI under https://dashboard.heroku.com/apps “New”
      
7. Write down the name of the application. You will need it in the next step
 
8. Deploy the jar using: `heroku deploy:jar my-app.jar --app APP_NAME`
9. Run `heroku open --app APP_NAME` or check the URL of deployed app under Domains: https://dashboard.heroku.com/apps/APP_NAME/settings

## Running from Github

- Add a [Frontend maven plugin](https://github.com/eirslett/frontend-maven-plugin) as a profile :
```
 <profile>
          <id>npm</id>
           <build>
               <plugins>
                 <plugin>
                    <groupId>com.github.eirslett</groupId>
                    <artifactId>frontend-maven-plugin</artifactId>
                    <!-- Use the latest released version:
                    https://repo1.maven.org/maven2/com/github/eirslett/frontend-maven-plugin/ -->
                    <version>1.9.1</version>
                    <executions>
                        <execution>
                            <!-- optional: you don't really need execution ids, but it looks nice in your build log. -->
                            <id>install node and npm</id>
                            <goals>
                                <goal>install-node-and-npm</goal>
                            </goals>
                            <!-- optional: default phase is "generate-resources" -->
                            <phase>generate-resources</phase>
                        </execution>
                    </executions>
                    <configuration>
                        <nodeVersion>v12.13.0</nodeVersion>
                    </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>

```

- Create and configure [Procfile](Procfile) in the root directory. Instructs Heroku which comments should be run on start-up
- Create [`heroku-settings.xml`](heroku-settings.xml) to instract maven what profiles should be enabled by default(then need to add a `MAVEN_SETTINGS_PATH` variable with the file name to the Config var in Settings section on Heroku)![Configure settins in Heroku](images/config_vars.JPG)
- Configure using two profiles (`production` and `npm` in this example) by default on application build in the `heroku-settings.xml`
- Associate the Github repo with the Heroku application(Create a new application in Heroku UI and choose Github as a source)
- It should work after that

## More Information

- [Vaadin Flow](https://vaadin.com/flow) documentation
- [Using Vaadin and Spring](https://vaadin.com/docs/v14/flow/spring/tutorial-spring-basic.html) article

### Running non-spring application

Example configurarion could be found from this pull request : [Add Heroku Configuration](https://github.com/vaadin/layout-examples/pull/28/files)

Procfile content :
```
web: java $JAVA_OPTS -jar target/dependency/webapp-runner.jar --port $PORT target/*.war
```

[Maven dependency plugin](https://maven.apache.org/plugins/maven-dependency-plugin/) (_The dependency plugin provides the capability to manipulate artifacts. It can copy and/or unpack artifacts from local or remote repositories to a specified location_): 
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>2.3</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals><goal>copy</goal></goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>com.heroku</groupId>
                        <artifactId>webapp-runner</artifactId>
                        <version>9.0.36.1</version>
                        <destFileName>webapp-runner.jar</destFileName>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin>
```

Also, you should add maven config to the Heroku app itself in its panel. The variable is : 
`MAVEN_CUSTOM_GOALS` : `clean package -Pproduction`

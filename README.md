# Project Base for Vaadin and Spring Boot

This simple project is used to verify how deplyment of Vaadin application works with Heroku. 
Since spring-boot application is packaged by default as a jar, there are three different deployment possibilites:

- Deploy a pre-build in production mode jar
- Deploy a pre-build in production mode war
- Associate a github repo with an app on Heroku service

All three are possible with vaadin.

## Running a jar

1. Create a Heroku account at https://www.heroku.com/home

2. Check that git is installed and if not, install git

3. Install Heroku CLI (https://devcenter.heroku.com/articles/heroku-cli)

4. Install Java CLI plugin (heroku plugins:install java)

5. Change the server.port in application.properties settings to `server.port=${port:8080}` (https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-change-the-location-of-external-properties)

6. Generate a jar from your application using package -Pproduction

7. Navigate to the folder, where generated jar is

    <i>Next steps are following the section here: [Using the Heroku Java CLI Plugin](https://devcenter.heroku.com/articles/deploying-executable-jar-files#using-the-heroku-java-cli-plugin)</i>

8. Create a Heroku application by either running
      - `heroku create --no-remote` in the CLI`
      - Or in the UI under https://dashboard.heroku.com/apps “New”
      
9. Write down the name of the application. You will need it in the next step
 
10. Deploy the jar using: heroku deploy:jar my-app.jar --app APP_NAME
11. Run heroku open --app APP_NAME or check the URL of deployed app under Domains: https://dashboard.heroku.com/apps/APP_NAME/settings

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

- Configure Procfile in the root directory
- Create `heroku-settings.xml` (then need to add a `MAVEN_SETTINGS_PATH` with the file name to the Config var in Settings section on Heroku)
- configure using two profiles (`production` and `npm` in this example) by default on application build
- Associate the Github repo with the Heroku application
- It should work after that

## More Information

- [Vaadin Flow](https://vaadin.com/flow) documentation
- [Using Vaadin and Spring](https://vaadin.com/docs/v14/flow/spring/tutorial-spring-basic.html) article


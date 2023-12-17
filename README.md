This project showcases the integration and configuration of the [java-semver-checker](https://github.com/trpouh/java-semver-checker) maven plugin. To try it out for yourself simply clone the repository and run the following commands:

```sh
git clone https://github.com/trpouh/jsc-example.git
cd jsc-example

# non breaking
mvn verify

# breaking
git checkout feature
mvn verify

# breaking - CLI
mvn semver-checker:check -Dbaseline.ref=main -Dcurrent.ref=feature 
```

The codebase has two branches:

* `main`
* `feature`

The project is structured like a simple maven project and contains one public class. The feature branch contains a commit that removes the public class (**breaking change!**)

The java-semver-checker maven plugin is [configured](https://github.com/trpouh/jsc-example/blob/main/pom.xml#L24) to check the current git `ref` (commit, branch, tag) against the `main` ref.

```xml
<configuration>
    <baselineRef>main</baselineRef>
</configuration>
```

it is also [configured](https://github.com/trpouh/jsc-example/blob/main/pom.xml#L32) to be executed in the `verify` phase of the maven project.

```xml
<executions>
    <execution>
        <id>check-semver</id>
        <goals>
            <goal>check</goal>
        </goals>
        <phase>validate</phase>
    </execution>
</executions>
```

Which results in a breaking build when trying to verify the project:

```shell
mvn verify
[...]
[ERROR] ------------------------------------------------------------------------
[ERROR] 
[ERROR] Found 1 Rule violation
[ERROR] 
[ERROR] Rule ClassMissingCheck (class has been renamed or removed): 1 violations
[ERROR] For more info see: https://github.com/trpouh/java-semver-checker#major-renamingmovingremoving-any-public-elements
[ERROR] 
[ERROR] Locations: 
[ERROR] - at.hks.semverchecker.example.Main
[ERROR] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.739 s
[INFO] Finished at: 2023-12-17T23:22:30+01:00
[INFO] ------------------------------------------------------------------------
```
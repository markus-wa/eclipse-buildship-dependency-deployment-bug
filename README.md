# Eclipse / Buildship `runtimeOnly` dependencies not deployed

This is a Minimal Working Example for a Eclipse Buildship bug where not all dependencies are deployed to Tomcat.

## Expected behaviour

`runtime-lib` and it's dependencies:
```gradle
dependencies {
	// Missing!
	api group: 'com.google.collections', name: 'google-collections', version: '1.0'

	// Missing!
	implementation 'com.google.guava:guava:23.0'
}
```
are deployed to `WEB-INF/lib` when deploying to Tomcat 8 from Eclipse

## Actual behaviour

`runtime-lib` and it's dependencies are missing from `WEB-INF/lib`

## Reproduction

1. Import this project as 'Existing Gradle Project' into Eclipse

2. Deploy to Tomcat from Eclipse

3. Right click on the Tomcat instance in the Servers view -> 'Browse Deployment Location' -> `webapp/WEB-INF/lib`

4. Notice the missing libs (`runtime-lib`, `google-collections` & `guava`)

## Observations / Suspection

`webapp/.settings/org.eclipse.wst.common.component` is missing  the `dependent-module` `runtime-lib`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project-modules id="moduleCoreId" project-version="1.5.0">
        <wb-module deploy-name="webapp">
                <property name="context-root" value="webapp"/>
                <wb-resource deploy-path="/WEB-INF/classes" source-path="src/main/resources"/>
                <wb-resource deploy-path="/WEB-INF/classes" source-path="src/main/groovy"/>
                <dependent-module deploy-path="/WEB-INF/lib" handle="module:/resource/compile-lib/compile-lib">
                        <dependency-type>uses</dependency-type>
                </dependent-module>
        </wb-module>
</project-modules>
```

This could be a bug in the gradle `eclipse-wtp` plugin which is responsible for generating this file.

But adding this doesn't solve the whole problem.
The dependencies of `runtime-lib` - `google-collections` & `guava` - are still missing.
I suspect this is because `webapp` is supposed to know about them by itself as they are listed in the 'Project and External Dependencies' in Eclipse.

## Thanks to [davidnortonjr](https://github.com/davidnortonjr)

Initial project layout taken from https://github.com/davidnortonjr/spring-boot-gradle-war

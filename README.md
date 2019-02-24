# AppWithCi
## What you should pay attention to:

## hierarchy
Repo stores the project root.

App module is stored in /app.

### .circleci/config.yml
Add as many jobs as you want.

Working directory = ~/<repo-name>

Docker image - choose correct api version, e.g circleci/android:api-28-alpha

I run detekt to check code quality before build:
```
  - run:
      name: Run detekt
      command: ./gradlew detekt
```
You can create special job for it.
```
- store_test_results:
      path: app/build/reports
```
Specify dir to store reports of detekt.

### detekt
add dependency to /build.gradle
```
plugins {
    id("io.gitlab.arturbosch.detekt").version("1.0.0-RC14")
}

dependencies {
    detektPlugins "io.gitlab.arturbosch.detekt:detekt-formatting:1.0.0-RC14"
}
```

add this to /app/build.gradle (module level build)
```  
apply plugin: 'io.gitlab.arturbosch.detekt' # adds detekt task

detekt {
    version = "1.0.0-RC14"
    
    # specify sources
    input = files("${project.rootDir}/${project.getName()}/src/main/")
    
    # path to detekt configuration
    config = files("${project.rootDir}/.circleci/detekt/detekt-config.yml")
    
    filters = ".*test.*,.*/resources/.*,.*/tmp/.*"
    # generates reports to build/reports!!! you should specify this path in
    # circle ci config as store_test_results: <this_path>
    reports {
        xml {
            enabled = true 
            destination = file("build/reports/detekt.xml")
        }
        html {
            enabled = true
            destination = file("build/reports/detekt.html")
        }
    }
}
```

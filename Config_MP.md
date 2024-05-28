---
title: Config MP
previous: getting-started/index.html
next: Context_MP.html
layout: page
---

# Configuration

## Overview
The majority of applications need to be configured based on a running environment.  Make sure the configuration data can be modified from outside the application so that you don’t need to repackage the application.  For example, the application may need to persist data in the database. You can manage the database connection information in the configuration.  Configuration data may come from different config sources, such as environment variables, system properties, configuration files, and so on.  A single merged view of the configuration data is presented to developers through the config APIs as key-value pairs.  Config sources are treated as a hierarchy, and the first source that has a specific configuration key wins, and its value is used.

Spectra config library provides consistent APIs for accessing the configuration for ** Helidon SE **, ** Helidon MP **, or ** Microprofile **.

The config module provides a few main functionalities:
  - A global configuration that can be accessed anywhere in your code.
  - Generic configuration APIs are not tied to particular implementation. For example, changing from Helidon SE to MP doesn't impact the code used in the configuration.
  - Conversion between implementation-specific configuration and generic Spectra configuration.

## MP Configuration

Configuration data might be loaded from multiple sources. 
  - In Helidon MP, by default, configurations are loaded from system properties, environment variables, and /META-INF/microprofile-config.properties found in the classpath.
    - System properties have the highest precedence, followed by environment variables, and then microprofile-config.properties.
    - Here's an example of a setting in the microprofile-config.properties file:
      ```
       app.greeting=HelloFromMPConfig
       app.page-size=20
      ```
  - Alternatively, you can use a config profile to declare the sources to load.
    - Helidon MP by default picks up mp-meta-config.yaml as the config profile from classpath.
    - To set the location of the config profile, use the io.helidon.config.mp.meta-config property or the HELIDON_MP_META_CONFIG environment variable.
    - Here's an example of the contents of the config profile. 
      ```
        add-default-sources: false

        sources:
          - type: "environment-variables"
            ordinal: 400
          - type: "system-properties"
            ordinal: 300
          - type: "yaml"
            classpath: "META-INF/default-config.yaml"
            ordinal: 100
          - type: "yaml"
            path: "./conf/prod.yaml"
            ordinal: 200
            optional: true
      ```
      Here’s an example of the contents of a config profile. The ordinal value for each configuration source determines the order of precedence in which Helidon uses them; the higher the value, the higher the priority. As per this configuration, Helidon will use  the configuration sources in the following order: 
      1. Environment variables 
      2. System properties
      3. A file from the ./conf/prod.yaml config profile
      4. The application.yaml file from the classpath

      
      **NOTE:**  The "ordinal" value determines the order/precedence of the source; the higher the value, the higher the priority.
      

For more information about Helidon Config Sources, see [Helidon MP Config Sources](https://helidon.io/docs/v2/#/mp/guides/03_config). 

### Maven Coordinates
    
    <dependency>
       <groupId>oracle.spectra.bundles</groupId>
       <artifactId>config-helidon-mp</artifactId>
    </dependency>

### Exports

    oracle.spectra.common.config   

### Usage
If you are using the default configuration sources or config profile, invoke this method to access the configuration:

    //the Spectra config will contain the MP config
    var config = oracle.spectra.common.config.ConfigProvider.config();
    var pageSize = config.getInt("app.page-size"); //or other getXXX APIs depending on the data type
# SpringBootEmailTools
A set of services and tools for sending emails in a Spring Boot application using Freemarker template engine.


## Dependency

```xml
<dependency>
    <groupId>spring-boot-utils</groupId>
    <artifactId>spring-boot-email</artifactId>
    <version>${release-version}</version>
</dependency>
```


## Usage
In your main Spring Boot application, you may need to add an explicit reference
to scan for all the services and controllers defined in the Spring Boot Email module, e.g.:

```java
package com.myapplication;

@SpringBootApplication
@ComponentScan(basePackages = {"com.myapplication", "open.springboot.mail"})
public class MainApplication  {

    public static void main(final String... args) {

    }
}
```

in you application.yml set the configuration needed to send the emails, e.g. if you want to send
the emails using a Gmail account you can set:

```yml
spring.mail.host: smtp.gmail.com
spring.mail.port: 587
spring.mail.username: name.surname@gmail.com
spring.mail.password: V3ry_Str0ng_Password
spring.mail.properties.mail.smtp.auth: true
spring.mail.properties.mail.smtp.starttls.enable: true
spring.mail.properties.mail.smtp.starttls.required: true
```


To send an email, use the ``EmailService`` in your Spring Boot application. E.g.


```java
@Autowired
public EmailService emailService;

public void sendEmailWithoutTemplating(){
   final Email email = EmailImpl.builder()
        .from(new InternetAddress("cicero@mala-tempora.currunt", "Marco Tullio Cicerone "))
        .to(Lists.newArrayList(new InternetAddress("titus@de-rerum.natura", "Pomponius Attĭcus")))
        .subject("Laelius de amicitia")
        .body("Firmamentum autem stabilitatis constantiaeque eius, quam in amicitia quaerimus, fides est.")
        .encoding(Charset.forName("UTF-8")).build();

   emailService.send(email);
}
```


The previous code will send a plain text message. To obtain some more dynamic fancy emails, you can use the Freemarker template engine. Just put the template in the required folder (e.g. ``templates`` under ``resourses``) and


```java
@Autowired
public EmailService emailService;

public void sendEmailWithTemplating(){
   Arrays.asList(new Cospirator("cassius@sic-semper.tyrannis", "Gaius Cassius Longinus"), 
            new Cospirator("brutus@sic-semper.tyrannis", "Marcus Iunius Brutus Caepio"))
        .stream.forEach(tyrannicida -> {
       final Email email = EmailImpl.builder()
            .from(new InternetAddress("divus.iulius@mala-tempora.currunt", "Gaius Iulius Caesar"))
            .to(Lists.newArrayList(new InternetAddress(tyrannicida.getEmail(), tyrannicida.getName())))
            .subject("Idus Martii")
            .encoding(Charset.forName("UTF-8")).build();
        //Defining the model object for the given Freemarker template    
        final Map<String, Object> modelObject = new HashMap<>();
        modelObject.put("tyrannicida", tyrannicida.getName());
    
       emailService.send(email, "idus_martii.ftl", modelObject);
   };
}

private static class Cospirator {
  private String email;
  private String name;
  public Cospirator(final String email, final String name){
    this.email = email;
    this.name = name;
  }
  
  //getters
}
```

where the template ``idus_martii.ftl`` is a Freemarker file like:

```html
<!doctype html>
<html>
	<body>
		<p>
			Tu quoque, <em>${tyrannicida}</em>!
		</p>
	</body>
</html>
```
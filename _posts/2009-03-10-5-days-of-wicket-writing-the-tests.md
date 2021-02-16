---
layout: post
status: publish
published: true
title: 5 Days of Wicket - Writing the tests
author:
  name: Johan Edström
  picture: '/assets/blog/authors/jedstrom.jpg'
  url: https://mysticcoders.com
  linkedin: /in/seijoed/
date: '2009-03-10T09:00:33.0000Z'
tags: []
comments: true
---
In the course of building an application, whether it due to shortened timelines, lack of process, or laziness, tests become an afterthought.  Tests often are added when problems are found, or to root out bugs that were not found during early phases.

This article, day 2 of our project, will show how to write a solid baseline for your application with tests as the strong foundation.  Our first meetings laid down the scope and direction we wanted achieved for this short project.  We had a clear sense of the problem we wanted to solve, as well as the basic components we were going to implement.
<!--more-->

In testing code there are several cycles commonly used in the enterprise community.  We will start our testing tutorial by talking about <a href="http://en.wikipedia.org/wiki/Mock_object" target="_blank">mocks</a>.

## Mocks
The Wikipedia page on Mock object describes them simply:

> In object-oriented programming, mock objects are simulated objects that mimic the behavior of real objects in controlled ways

In Java, utilizing Mock objects generally requires the usage of interfaces.  The interfaces are hopefully something that evolved from initial design discussions, and become the basis for the expected functions and behavior.

The tool we use to build the Mocks is <a href="http://www.easymock.org/" target="_blank">EasyMock</a> and <a href="http://www.junit.org/" target="_blank">JUnit</a> for unit tests.  In your search for the ultimate testing tool, I'd strongly encourage you to try out other tools such as <a href="http://testng.org/" target="_blank">TestNG</a> and <a href="http://jmock.org" target="_blank">JMock</a>.

The longest running unit-testing tool in the Java world is definitely JUnit.  If you'd like to read up on Test Driven Development and each of the concepts outlined here in more detail, <a href="http://www.amazon.com/Test-Driven-Development-Addison-Wesley-Signature/dp/0321146530/ref=pd_bbs_sr_1?ie=UTF8&amp;s=books&amp;qid=1236382900&amp;sr=8-1">Test Driven Development by Example</a> is a brilliant book written by Kent Beck on the topic.  Test Driven Development stands alone in the Agile theory as the most effective value preached, especially when done early, and done comprehensively.

We have a few interfaces now, which came of initial discussion about how we imagine a pastebin functioning, so what is the next step?  And how can testing help us?

Here is the primary service interface:

``` java
public interface PasteService {
	List getLatestItems(String clientToken, int count, boolean threaded) throws InvalidClientException;

	PasteItem getItem(String clientToken, long id) throws InvalidClientException;

	PasteItem findPrivateItem(String clientToken, String privateToken) throws InvalidClientException;

	List findItemsByLanguage(String clientToken, LanguageType languageType, int count, boolean threaded) throws InvalidClientException;

	long createItem(String clientToken, PasteItem item) throws InvalidClientException;

	long createReplyItem(String clientToken, PasteItem item, long parentId) throws InvalidClientException, ParentNotFoundException;

	List getItemsForUser(String clientToken, String userToken) throws InvalidClientException;

	PasteStats getStats(String clientToken) throws InvalidClientException;
}
```

This is the first step in the design, we now have something describing the desired functionality, so we will start by mocking these interfaces.

EasyMock is a handy tool for this, so we simply add the necessary bits and pieces to our pom file and reload the project to get the correct dependencies:

``` xml
<dependency>
    <groupId>org.easymock</groupId>
    <artifactId>easymock</artifactId>
    <version>2.4</version>
</dependency>
```

``` xml
<dependency>
    <groupId>org.easymock</groupId>
    <artifactId>easymockclassextension</artifactId>
    <version>2.4</version>
</dependency>
```

We've also made some changes to the initial POM that we created to pull in JUnit version 4.5.  Newer versions of JUnit are backwards compatible and add enhancements to your testing such as annotations.

For our first mocks, we start off with extending JUnit's default <em>TestCase</em>.  We add in some convenience methods to help us return a mock Dao and a mock Service.

``` java
private PasteItemDao dao;
	private PasteService svc;

	@Test
	public void testMocks() {
		dao = getPasteItemDao();
	 	assertNotNull(getPasteItemDao());
	 	svc = getPasteService();
	 	assertNotNull(getPasteService());
	}

	@Test
	public void testGetLatestItems() throws Exception {
		svc = getPasteService();
		assertNotNull(getPasteService());
		expect(svc.getLatestItems("CLIENT", 10, false)).andReturn(getPastes(10, LanguageType.PASCAL, false));
		replay(svc);
		List returnedList = svc.getLatestItems("CLIENT", 10, false);
		verify(svc);
		assertEquals(10, returnedList.size());
	}
```

What we are doing here is essentially describing the calls we expect, we replay and call the mocked service and finally we verify and assert that the results are as expected.

While this may seem quite obvious, this will help us in comparing what an implementation will have to return, that the code has coverage and the mocks of these classes actually behave as expected. Once we have full mock coverage we can start implementing the code, and keep looping back to our tests to verify that things work as expected.

## Integration and Implementation Tests
Once we have a working implementation, covered by our mock tests for expected behavior, the next step to consider are <a href="http://en.wikipedia.org/wiki/Integration_testing" target="_blank">integration tests</a>.  Integration tests involve a more complete test involving third party components such as a database or complex services.  The focus here is to track down potential errors in our project e.g.  configuration files, Dao mapping and entity problems, etc.  The tool we consider well thought out and best of breed would help us setup a complete testbed, to include an in-memory database.

<a href="http://unitils.org" target="_blank">Unitils</a> brings together several common tools used separately into a cohesive integration testing framework.  It provides the ability to run a Spring context and an in-memory database which we can use to perform full life-cycle tests on all components.  These tools let us write tests that will verify our applications behaviour in a real life situation.  The value here can be seen later in the project, as it will continue to be referenced throughout the lifecycle of our project.  We can use them to hunt down possible bugs, add features without worry of breaking other pieces of our application, or simply maintaining and refactoring the codebase.  Yet another benefit is hidden, for it helps new developers on the project understand how everything ties together without the need for a separate, distant, and possibly rarely updated document.

We hunt down our applications Spring configuration file "applicationContext.xml" and make a copy of it and place it in "src/test/resources".  In addition we add a secondary configuration file "applicationContext-test.xml" that overrides the default datasource and supplies a Unitils datasource.

applicationContext-test.xml

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">

   <bean id="dataSource" class="org.unitils.database.UnitilsDataSourceFactoryBean"/>

</beans>
```

We then add a file "unitils.properties" where we define how unitils is supposed to behave.

``` java
# Properties for the PropertiesDataSourceFactory
database.driverClassName=org.hsqldb.jdbcDriver

database.url=jdbc:hsqldb:mem:mysticpaste-test
database.userName=sa
database.password=

# This property specifies the underlying DBMS implementation. Supported values are 'oracle', 'db2', 'mysql', 'hsqldb' and 'postgresql'.
# The value of this property defines which vendor specific implementations of DbSupport and ConstraintsDisabler are chosen.
database.dialect=hsqldb

## This property specifies the database schema that is used. This schema name is used to qualify all tables when (amongst
# others) clearing / dropping tables / inserting test data.
# NOTE: schema name is case sensitive
database.schemaName=mysticpaste
dbMaintainer.generateDTD.enabled=TRUE
# DbUnit database DTD file path
dtdGenerator.dtd.filename=src/resources/test.dtd
```

This file will now allow us to move on to an implementation we start with an AbstractBaseTest where we extend a Unitils class called UnitilsJUnit4.

``` java
public class AbstractIntegrationTest extends UnitilsJUnit4 {

    @SpringApplicationContext({"applicationContext.xml", "applicationContext-test.xml"})
    private ApplicationContext applicationContext;

    /**
    * Method testDoNothing ...
    */
    //This is to keep maven surefire quiet.
    @Test
    public void testDoNothing() {
        //Nothing
    }

}
```

If you run the test, you will see in the logging output a complete invocation of the spring context, database setup and verification of mapped entities, pretty nifty for an empty test!

We can now move on to testing the actual implementations provided with a full cycle environment.

As we are using Unitils and its Spring utilities we also have handy annotations for injecting our real daos and their implementations into our tests.

Our final test-class that will completely test our environment looks as follows:

``` java
public class PasteServiceIntegrationTest extends AbstractIntegrationTest {
    @SpringBeanByType
    private PasteService svc;
    @SpringBeanByType
    private PasteItemDao dao;
    // We start with verifying that the DB is correctly setup.
    @Test
    public void testMapping() {
        HibernateUnitils.assertMappingWithDatabaseConsistent();
    }
    @Test
    public void testGetCurrentCount() {
        PasteItem paste = new PasteItem();
        paste.setContent("TEST-DATA");
        paste.setUserToken("USER");
        paste.setClientToken("CLIENT");
        try {
            svc.createItem("CLIENT", paste);
            assertTrue(svc.getItemsForUser("CLIENT", "USER").size() == 1);
        } catch (InvalidClientException e) {
            e.printStackTrace();  //TODO
        }
    }
    @Test
    public void testCreateAndRetrieve() throws InvalidClientException {
        PasteItem paste = new PasteItem();
        paste.setContent("TEST-DATA");
        paste.setUserToken("USER");
        paste.setClientToken("CLIENT");
        Long id = svc.createItem("CLIENT", paste);
        System.out.println(id);
        PasteItem item2 = svc.getItem("CLIENT", id);
        assertEquals(item2.getClientToken(), paste.getClientToken());
    }
}
```

Voila! We have a complete life cycle test. These tests of course run somewhat slower than mocks. As tools and aids to rapid development they are quite valuable and will help find elusive bugs and problematic issues.

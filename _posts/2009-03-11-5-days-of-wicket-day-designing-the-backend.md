---
layout: post
status: publish
published: true
title: 5 Days of Wicket - Designing the backend
author:
  name: Guillermo Castro
  picture: '/assets/blog/authors/gcastro.jpg'
  url: https://mysticcoders.com
  linkedin: /in/javageek
date: '2009-03-11T09:00:05.0000Z'
tags: []
comments: true
---
As mentioned in <a href="/blog/5-days-of-wicket-writing-the-tests">day 2</a> of our series, we have a main service interface that came from an initial discussion of what we think a pastebin should do.&Acirc;&nbsp;What can be easier than creating an entity model for a pastebin application, right? It's just a big text area that holds a piece of text (content) that someone wants to share with the rest of the world, or at least with someone else who might be interested in looking at it. However, there are some specific things that we wanted to accomplish in our application that came from a series of ideas by the different team members. Of course, although not really a requirement, we wanted to build this application using <a href="http://wicket.apache.org/" target="_blank">Apache Wicket</a>.

<!--more-->

<h1>Requirements</h1>
Here is a list of the ideas (or requirements) we wanted for our pastebin, in no particular order:

<ul>
<li>A paste item must be identified by a unique id and/or timestamp, and it should contain text and optionally a language identifier (useful for syntax highlighting).</li>
<li>A Paste item may be a Reply of another Paste item (can have a parent item).</li>
<li>A Paste item may have one or more replies (children items).</li>
<li>A Paste item may be private. Private items will be identified by a special random string token of a defined length.</li>
<li>A Paste item may be associated with a specific user (author).</li>
<li>A given author that has been identified to the system will be able to see all the paste items that he/she created.</li>
</ul>

<h1>Models/Entities</h1>
From the set of requirements we can get a sense of how the entities are going to be created and how they're going to relate to each other, as seen in the following diagram:

<img class="aligncenter size-full wp-image-425" title="Class Diagram" src="https://www.mysticcoders.com/wp-content/uploads/2009/03/class-diagram.png" alt="Class Diagram" width="452" height="194" />

This looks like a very simple entity model, but it fits with what we want to accomplish for this particular project. During the brainstorming session many features were discussed, like the ability to edit and/or delete an item that you own (identified by some session token saved in a cookie), the ability to upload images, have an API for external clients that want access to the pastebin, etc. This might be implemented in the future, but were omitted for the first iteration of the project. However, some of the basis for the functionalities is there, like having a client token to identify the different clients accessing the server and be able to show them in the future.

So now that we have a base, let's get to do some actual coding. As mentioned before, we decided on using <a href="http://www.hibernate.org/" target="_blank">Hibernate</a> as our ORM mapping. This is how we define the PasteItem class (the getters and setters have been omitted from the class to make it easier to read):

``` java
import javax.persistence.Basic;
import javax.persistence.Column;
import javax.persistence.Entity;
import static javax.persistence.EnumType.STRING;
import javax.persistence.Enumerated;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.Lob;
import javax.persistence.ManyToOne;
import javax.persistence.NamedQueries;
import javax.persistence.NamedQuery;
import javax.persistence.OneToMany;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import java.io.Serializable;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@Entity
@NamedQueries({@NamedQuery(name = "item.getById", query = "from PasteItem item where item.id = :id"),
        @NamedQuery(name = "item.find",
                query = "from PasteItem item where item.isPrivate != true order by item.timestamp desc"),
        @NamedQuery(name = "item.findThreaded",
                query = "from PasteItem item where item.isPrivate != true and item.parent is null order by item.timestamp desc"),
        @NamedQuery(name = "item.findByLanguage",
                query = "from PasteItem item where item.isPrivate != true and item.type = :type order by item.timestamp desc"),
        @NamedQuery(name = "item.findByLanguageThreaded",
                query = "from PasteItem item where item.isPrivate != true and item.parent is null and item.type = :type order by item.timestamp desc"),
        @NamedQuery(name = "item.findByToken", query = "from PasteItem item where item.privateToken = :token"),
        @NamedQuery(name = "item.findByUser",
                query = "from PasteItem item where item.isPrivate != true and item.userToken = :token"),
        @NamedQuery(name = "item.count", query = "select count(*) from PasteItem item where item.isPrivate != true")})
public class PasteItem implements Serializable {
    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    protected long id;

    @Lob
    protected String content;

    @Enumerated(STRING)
    protected LanguageType type;

    @Temporal(TemporalType.TIMESTAMP)
    protected Date timestamp;

    @Basic
    protected String userToken;

    @Basic
    protected String clientToken;

    @Basic
    protected boolean isPrivate;

    @Basic
    @Column(name = "PRIVATE_TOKEN", unique = true, updatable = false)
    protected String privateToken;

    @ManyToOne(fetch = FetchType.LAZY, optional = true)
    @JoinColumn(name = "PARENT_ITEM_ID", nullable = true)
    protected PasteItem parent;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "parent")
    protected List<PasteItem> children;
    ...
}
```

We are using annotations to define our entity model. The interesting part here is that we didn't use any <a href="http://www.hibernate.org/397.html" target="_blank">Hibernate-specific annotations</a>, and instead relied on the standard ones from the <a href="http://java.sun.com/developer/technicalArticles/J2EE/jpa/" target="_blank">JPA API</a>. Hibernate understands these annotations so it seems logical to use them because then we can make the application more portable, as we could replace Hibernate for any JPA-enabled library. Also, another great feature of JPA is that we can define a set of named queries that our Dao implementations can use. This allows you to define almost anything that's related to the persistence layer under one class, like the entity, its properties and the different ways to access the entity via queries under one place, which then becomes your main reference class.

<h1>Service layer</h1>
We defined a primary service interface that the Wicket front-end will use to interact between the web pages and the rest of the application. The service layer classes are usually responsible for implementing the required business rules of the application, and they can be as complex or as simple as required, depending on what the actual requirements of the application are. Wikipedia defines a <a href="http://en.wikipedia.org/wiki/Business_rule" target="_blank">business rule</a> as:

<blockquote><strong>Business rule</strong> is a statement that defines or constrains some aspect of the business. It is intended to assert business structure or to control or influence the behavior of the business. Individual business rules that describe the same facet of an enterprise are usually arranged into&Acirc;&nbsp;<strong>business rulesets</strong>. Business rules describe the operations, definitions and constraints that apply to an organization in achieving its goals.</blockquote>
In layman's terms, a business rule is anything that affects the way we act on our data. This is how our service implementation looks like:

``` java
import com.mysticcoders.mysticpaste.model.LanguageType;
import com.mysticcoders.mysticpaste.model.PasteItem;
import com.mysticcoders.mysticpaste.persistence.PasteItemDao;
import com.mysticcoders.mysticpaste.utils.TokenGenerator;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.transaction.annotation.Transactional;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class PasteServiceImpl implements PasteService {
    private final Logger logger = LoggerFactory.getLogger(getClass());

    public static final int DEFAULT_TOKEN_LENGTH = 10;

    private PasteItemDao itemDao;

    private int tokenLength;

    public PasteServiceImpl() {
        this.tokenLength = DEFAULT_TOKEN_LENGTH;
    }

    public PasteServiceImpl(PasteItemDao itemDao, int tokenLength) {
        this.itemDao = itemDao;
        this.tokenLength = tokenLength;
    }

    @Transactional(readOnly = true)
    public List<PasteItem> getLatestItems(String clientToken, int count, int startIndex, boolean threaded)
            throws InvalidClientException {
        logger.trace("Service: getLatestItems. clientToken = {}, count = {}, startIndex = {}, threaded = {}",
                new Object[]{clientToken, count, startIndex, threaded});
        List<PasteItem> results = null;
        if (threaded) {
            results = itemDao.findThreaded(count, startIndex);
        } else {
            results = itemDao.find(count, startIndex);
        }

        if (null == results) {
            logger.warn("Found no items in database.");
            results = new ArrayList<PasteItem>();
        }
        return results;
    }

    @Transactional(readOnly = true)
    public PasteItem getItem(String clientToken, long id) throws InvalidClientException {
        return itemDao.get(id);
    }

    @Transactional(readOnly = true)
    public PasteItem findPrivateItem(String clientToken, String privateToken) throws InvalidClientException {
        return itemDao.findByToken(privateToken);
    }

    @Transactional(readOnly = true)
    public List<PasteItem> findItemsByLanguage(String clientToken, LanguageType languageType, int count,
                                               int startIndex, boolean threaded)
            throws InvalidClientException {

        List<PasteItem> results = null;
        if (threaded) {
            results = itemDao.findByLanguageThreaded(languageType, count, startIndex);
        } else {
            results = itemDao.findByLanguage(languageType, count, startIndex);
        }
        if (null == results) {
            results = new ArrayList<PasteItem>();
        }
        return results;
    }

    @Transactional(rollbackFor = Exception.class)
    public long createItem(String clientToken, PasteItem item) throws InvalidClientException {
        if (null != item &amp;&amp; item.isPrivate()) {
            item.setPrivateToken(TokenGenerator.generateToken(getTokenLength()));
        }
        // set created Timestamp
        item.setTimestamp(new Date(System.currentTimeMillis()));
        return itemDao.create(item);
    }

    public long getLatestItemsCount(String clientToken) throws InvalidClientException {
        return itemDao.getPasteCount();
    }

    public PasteItemDao getItemDao() {
        return itemDao;
    }

    public void setItemDao(PasteItemDao itemDao) {
        this.itemDao = itemDao;
    }

    public int getTokenLength() {
        return tokenLength;
    }

    public void setTokenLength(int tokenLength) {
        this.tokenLength = tokenLength;
    }
}
```

The business rules for the pastebin are very light in nature. One of the business rule we have from the requirements is to generate a random string token to use as the private paste identifier, so that instead of having a sequential id (which can be guessed), it is identified by this string and a special url.

We're using <a href="http://www.slf4j.org/" target="_blank">slf4j</a> as our logging mechanism. This allows us to statically map our logging to one of log4j, jdk, etc., and it also allows us to have very simple logging messages that will help us 'debug' the application in a sense. Nowadays it is considered bad practice to use <em>System.out.println()</em> messages as we don't have control over them (i.e. they will always appear). Having a logging mechanism with separate message levels allows us to control what we want to show.

It is also interesting to note that in order to support a transactional set of methods, we used Spring's <a href="http://static.springsource.org/spring/docs/2.5.1/api/org/springframework/transaction/annotation/Transactional.html" target="_blank">@Transactional</a> annotation. This annotation allows us to mark a method (and its underlying method calls) as part of one transaction, and optionally to mark said transaction as read-only. Also, take care that marking a method as transactional is often not enough, as we have to specify under which conditions the transaction needs to rollback (in our case, every time an exception is thrown). This is because by default, spring only rolls back transactions when <strong>runtime</strong> exceptions are thrown.

<h1>Persistence layer</h1>
Since we are using Hibernate, our persistence layer becomes a really easy, thin layer. Here's our Dao implementation:

``` java
import com.mysticcoders.mysticpaste.model.LanguageType;
import com.mysticcoders.mysticpaste.model.PasteItem;
import com.mysticcoders.mysticpaste.persistence.PasteItemDao;

import java.util.List;

public class PasteItemDaoImpl extends AbstractDaoHibernate<PasteItem> implements PasteItemDao {

    protected PasteItemDaoImpl() {
        super(PasteItem.class);
    }

    public Long create(PasteItem item) {
        save(item);
        return item.getId();
    }

    public PasteItem get(long id) {
        PasteItem item = (PasteItem) getSession().getNamedQuery("item.getById")
                .setLong("id", id).setMaxResults(1)
                .uniqueResult();
        return item;
    }

    public List<PasteItem> findByLanguage(LanguageType languageType, int count, int startIndex) {
        return getSession()
                .getNamedQuery("item.findByLanguage")
                .setParameter("type", languageType)
                .setMaxResults(count).setFirstResult(startIndex).list();
    }

    public List<PasteItem> findByLanguageThreaded(LanguageType languageType, int count, int startIndex) {
        return getSession()
                .getNamedQuery("item.findByLanguageThreaded")
                .setParameter("type", languageType)
                .setMaxResults(count).setFirstResult(startIndex).list();
    }

    public List<PasteItem> find(int count, int startIndex) {
        return getSession().getNamedQuery("item.find")
                .setMaxResults(count).setFirstResult(startIndex).list();
    }

    public List<PasteItem> findThreaded(int count, int startIndex) {
        return getSession()
                .getNamedQuery("item.findThreaded")
                .setMaxResults(count).setFirstResult(startIndex).list();
    }

    public PasteItem findByToken(String privateToken) {
        return (PasteItem) getSession()
                .getNamedQuery("item.findByToken")
                .setParameter("token", privateToken)
                .uniqueResult();
    }

    public long getPasteCount() {
        Long count = (Long) getSession()
                .getNamedQuery("item.count")
                .setMaxResults(1).uniqueResult();
        return null == count ? 0 : count;
    }
}
```

We have taken advantage of JPA's <a href="http://java.sun.com/javaee/5/docs/api/javax/persistence/NamedQuery.html" target="_blank">@NamedQuery</a> annotation to greatly simplify the code in our Dao implementation. Since all the queries for accessing a PasteItem were already defined inside the entity (in our case, the PasteItem class), we only need to refer to those queries here, set the named parameters and get the results.

We also defined an abstract class to "generify" (term borrowed from IDEA's inspection) Hibernate's access:

``` java
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.springframework.orm.hibernate3.support.HibernateDaoSupport;

import java.io.Serializable;

public class AbstractDaoHibernate<T> extends HibernateDaoSupport {
    private Class entityClass;

    private SessionFactory sessionFactory;

    protected AbstractDaoHibernate(Class dataClass) {
        super();
        this.entityClass = dataClass;
    }

    @SuppressWarnings("unchecked")
    private T load(Long id) {
        return (T) getSession().get(entityClass, id);
    }

    @SuppressWarnings("unchecked")
    private T loadChecked(Long id) throws EntityNotFoundException {
        T persistedObject = load(id);

        if (persistedObject == null) {
            throw new EntityNotFoundException(entityClass, id);
        }
        return persistedObject;
    }

    public void merge(T detachedObject) {
        getSession().merge(detachedObject);
    }

    public void save(T persistedObject) {
        getSession().saveOrUpdate(persistedObject);
    }

    private void delete(T persistedObject) {
        getSession().delete(persistedObject);
    }

    public void delete(Long id) {
        delete(loadChecked(id));
    }
}
```

This class takes advantage of Java 5 generics in order to implement the common persistence methods that we have. It also extends Spring's <a href="http://static.springsource.org/spring/docs/2.5.x/api/org/springframework/orm/hibernate3/support/HibernateDaoSupport.html" target="_blank">HibernateDaoSupport</a> to make it easier to integrate with Spring.

<h1>Wiring everything together</h1>
Once we have the service and the persistence layer, we need a way to put everything together in order for our application to work. Since we are using Spring, we only need to define our beans in the applicationContext.xml:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">

    <!-- Services Beans -->
    <bean id="pasteService" class="com.mysticcoders.mysticpaste.services.PasteServiceImpl">

<property name="itemDao" ref="pasteItemDao"/>
<property name="tokenLength" value="${private.token.length}" />
    </bean>

    <!-- DAOs -->
    <bean id="pasteItemDao" class="com.mysticcoders.mysticpaste.persistence.hibernate.PasteItemDaoImpl">

<property name="sessionFactory" ref="sessionFactory"/>
    </bean>

    <!-- Wicket Application -->
    <bean id="wicketApplication" class="com.mysticcoders.mysticpaste.MysticPasteApplication"/>

    <!--  Database Beans -->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">

<property name="driverClassName" value="${jdbc.driver}"/>
<property name="url" value="${jdbc.url}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
    </bean>

    <!-- Hibernate session factory -->
    <bean id="sessionFactory"
          class="org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean">

<property name="dataSource" ref="dataSource"/>
<property name="hibernateProperties">
<props>
<prop key="hibernate.dialect">${hibernate.dialect}</prop>

<prop key="hibernate.show_sql">${hibernate.show_sql}</prop>

<prop key="use_outer_join">${hibernate.use_outer_join}</prop>

<prop key="hibernate.cache.use_second_level_cache">${hibernate.cache.use_second_level_cache}</prop>

<prop key="hibernate.cache.use_query_cache">${hibernate.cache.use_query_cache}</prop>

<prop key="hibernate.cache.provider_class">${hibernate.cache.provider}</prop>

<prop key="hibernate.connection.pool_size">10</prop>

<prop key="hibernate.jdbc.batch_size">1000</prop>

<prop key="hibernate.bytecode.use_reflection_optimizer">true</prop>

            </props>
        </property>

<property name="annotatedClasses">
<list>
                <value>com.mysticcoders.mysticpaste.model.PasteItem</value>
            </list>
        </property>

<property name="schemaUpdate" value="${hibernate.schemaUpdate}"/>
    </bean>

    <!-- Tell Spring it should use @Transactional annotations -->
    <tx:annotation-driven/>

    <bean id="transactionManager"
          class="org.springframework.orm.hibernate3.HibernateTransactionManager">

<property name="sessionFactory" ref="sessionFactory"/>
    </bean>
</beans>
```

The Service is configured as a bean, with a reference to the Dao implementation and to a variable that will be replaced by maven when building for the appropriate platform, as mentioned in <a href="/blog/5-days-of-wicket-day-1">day 1</a>. The Dao is configured with a reference to Hibernate's <em>sessionFactory</em> bean, and the rest is the configuration for Hibernate (the data source, the transaction manager, etc.).
Since we're using annotations, we need to set the property <em>annotatedClasses</em> with a list of the Hibernate (or JPA) configured entity classes, in this case the PasteItem class. In order for the @Transactional annotation to work we need to tell Spring that our transaction is driven by those annotations with the <strong><tx:annotation-driven/></strong> tag. I've been involved in previous projects where the other developers think they are doing transactions because they used the annotation, but forgot to add this piece to the configuration, thus resulting in database inconsistencies.

<h1>Conclusion</h1>
Designing the backend involves very little Wicket (or nothing at all as we saw here), but it's very important to separate our different layers to make the application easier to maintain. Using Spring and Hibernate is win-win situation because we leave many configuration options to Spring, and we are able to provide an easy to use and easy to understand implementation of our service and persistence layer.

<strong>EDIT:</strong> Added the wicketApplication bean to the Spring configuration file.

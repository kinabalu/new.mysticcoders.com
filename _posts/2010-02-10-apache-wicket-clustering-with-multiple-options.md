---
layout: post
status: publish
published: true
title: Apache Wicket Clustering With Multiple Options
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2010-02-10T22:23:35.0000Z'
tags: []
comments: true
---
We've been seeing more and more implementations of <a href="http://wicketbyexample.com/api/wicket/1.4.5/org/apache/wicket/protocol/http/SecondLevelCacheSessionStore.IPageStore.html" target="_blank">IPageStore</a> out in the wild for <a href="http://wicket.apache.org/" target="_blank">Apache Wicket</a>.  The interface basically decides how Wicket will store the <a href="http://cwiki.apache.org/WICKET/page-maps.html" target="_blank">Pagemap</a> for your application.  The default that ships with Wicket uses <a href="http://wicketbyexample.com/api/wicket/1.4.6/org/apache/wicket/protocol/http/pagestore/DiskPageStore.html" target="_blank">DiskPageStore</a> which is an implementation that stores the serialized pages grouped in a single file per pagemap. After reading a wonderful blog post on <a href="http://letsgetdugg.com" target="_blank">Letsgetdugg</a> a few days ago: <a href="http://letsgetdugg.com/2010/02/07/clustering-wicket-for-fun-and-profit/" target="_blank">Clustering Wicket for fun and profit!</a>, I decided to take a look at writing an implementation using <a href="http://hazelcast.com" target="_blank">Hazelcast</a> - an "open source clustering and highly scalable data distribution platform".
<a id="more"></a><a id="more-1726"></a>
The implementation below borrows heavily from <a href="http://fabulously40.com/fabulously/victori" target="_blank">Victor</a>.  It basically creates a HazelcastInstance in the constructor and then overrides all the methods necessary from <a href="http://wicketbyexample.com/api/wicket/1.4.5/org/apache/wicket/protocol/http/pagestore/AbstractPageStore.html" target="_blank">AbstractPageStore</a>.  Here's some quick code to put in your app's Application implementation that will use this new IPageStore:

``` java
    @Override
    protected ISessionStore newSessionStore() {
        return new SecondLevelCacheSessionStore(this,
                new HazelcastPageStore("default"));
    }
```

And here's the code for HazelcastPageStore:

``` java
public class HazelcastPageStore extends AbstractPageStore
                                      implements SecondLevelCacheSessionStore.IClusteredPageStore {

    private Logger logger = LoggerFactory.getLogger(HazelcastPageStore.class);

    private String mapName;
    private HazelcastInstance client;

    public HazelcastPageStore(String mapName) { this(mapName, null); }

    public HazelcastPageStore(String mapName, Config config) {
        this.mapName = mapName;
        client = Hazelcast.newHazelcastInstance(config);
    }

    public String getKey(final String sessId, final String pageMapName, final int pageId, final int pageVersion) {
        return getKey(sessId, pageMapName, pageId, pageVersion, -1);
    }

    public String getKey(final String sessId, final String pageMapName, final int pageId, final int pageVersion, final int ajaxVersion) {
        String key = sessId + ":" + pageMapName + ":" + pageId + ":" + pageVersion + ":" + ajaxVersion;
        if (logger.isDebugEnabled()) {
            logger.debug("GetKey: " + key);
        }
        return key;
    }

    public String storeKey(final String sessionId, final Page page) {
        String key = sessionId + ":" + page.getPageMapName() + ":" + page.getId() + ":" + page.getCurrentVersionNumber() + ":" + (page.getAjaxVersionNumber() - 1);
        if (logger.isDebugEnabled()) {
            logger.debug("StoreKey: " + key);
        }
        return key;
    }

    public boolean containsPage(final String sessionId, final String pageMapName, final int pageId, final int pageVersion) {
        return client.getMap(mapName).containsKey(getKey(sessionId, pageMapName, pageId, pageVersion));
    }

    public void destroy() {
        client.shutdown();
    }

    public <t> Page getPage(final String sessionId, final String pagemap, final int id, final int versionNumber, final int ajaxVersionNumber) {
        IMap<string, Page> map = client.getMap(mapName);

        return map.get(getKey(sessionId, pagemap, id, versionNumber, ajaxVersionNumber));
    }

    public void pageAccessed(final String sessionId, final Page page) {
    }

    public void removePage(final String sessionId, final String pagemap, final int id) {
        String key = getKey(sessionId, pagemap, id, 0);
        key = key.substring(0, key.lastIndexOf(":"));

        IMap<string, Page> map = Hazelcast.getMap(mapName);
        for (String k : map.keySet()) {
            if (k.startsWith(key)) {
                map.remove(k);
            }
        }
    }

    public void storePage(final String sessionId, final Page page) {
        IMap<string, Page> map = client.getMap(mapName);
        map.put(storeKey(sessionId, page), page);
    }

    public void unbind(final String sessionId) {
        IMap<string, Page> map = client.getMap(mapName);
        for (String key : map.keySet()) {
            if (key.startsWith(sessionId)) {
                map.remove(key);
            }
        }
    }
}
```

Several other IPageStore implementations available:

<ul>
<li><a href="http://letsgetdugg.com/2010/02/07/clustering-wicket-for-fun-and-profit/" target="_blank">Letsgetdugg - memcached implementation</a></li>
<li><a href="http://www.mail-archive.com/users@wicket.apache.org/msg46421.html" target="_blank">Google App Engine memcached implementation</a></li>
</ul>
Let us know if you find any others out in the wild so we can add them here.

---
title: "How we fixed a Node.js memory leak by using ShadowReader to replay production traffic into QA"
url: "https://technology.edmunds.com/2018/08/25/Investigating-a-Memory-Leak-and-Introducing-ShadowReader/"
date: "2018-08-25"
feed_url: "https://technology.edmunds.com/atom.xml"
---
Introduction At Edmunds, we recently moved our front end stack to a monolithic, isomorphic application based on Node.js, Express, and React. In the past, we relied on Java applications using a microservices architecture. While the migration to JavaScript based technology has given us many benefits, we are still battle testing it.

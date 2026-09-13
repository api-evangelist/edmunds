---
title: "Edmunds' GraphQL Federation Adoption (Part 2)"
url: "https://technology.edmunds.com/2022/10/27/Edmunds-GraphQL-Federation-Adoption-part-2/"
date: "2022-10-27"
feed_url: "https://technology.edmunds.com/atom.xml"
---
Yuhan Zhang, Suresh Narasimhan In Part 1 of this article, we talked about our motivation for adopting GraphQL Federation and how we restructured our existing REST apps into subgraphs to connect to a supergraph with GraphQL Federation. Individual teams owned the subgraphs, and as the number of subgraphs grew, GraphQL deployment became critical. As teams created and evolved their subgraphs we wanted the GraphQL deployment infrastructure to catch compatibility, performance issues and enforce standards.

# Fivetran Makes NetSuite’s API Usable | Fivetran Blog

Created: Apr 20, 2020 1:32 PM
URL: https://fivetran.com/blog/netsuite-connector-article

Understand the history of the Fivetran NetSuite connector and you'll see why you shouldn't try building your own.

![Fivetran%20Makes%20NetSuite%E2%80%99s%20API%20Usable%20Fivetran%20Blog%2037f56c35bfc948028ac6221e34ac8422/netsuite-connector-article.png](Fivetran%20Makes%20NetSuite%E2%80%99s%20API%20Usable%20Fivetran%20Blog%2037f56c35bfc948028ac6221e34ac8422/netsuite-connector-article.png)

NetSuite is one of the premier providers of ERP software, billing itself as a cloud-based [replacement for](http://www.netsuite.com/portal/solutions.shtml) QuickBooks, Microsoft Dynamics GP, SAP and others. Roughly 40,000 firms globally use NetSuite, and of those, a modest but growing number are customers of Fivetran. The NetSuite connector is currently a major selling point for Fivetran, not only because of its general usefulness as a piece of software but also because of the complete and utter intractability of its API.

## The NetSuite API Is a Scrambled Mess

Software designed to serve a wide range of business operations is inherently complicated. When this complexity is combined with an indifferent approach to documentation and a cavalier disregard for naming conventions, the result is a virtually unusable API.

As the Fivetran team began to build the NetSuite connector using the standard SOAP API, we quickly realized that there was a three-way disconnect between the software’s UI elements, documentation, and data feed. Furthermore, the data feed itself consisted of thousands of denormalized tables. The two initial iterations of the NetSuite connector together took a year and a half to build.

The current iteration of the NetSuite connector took an additional year to complete. It uses the more expensive, but better documented, JDBC API. The JDBC API offers some access to the underlying database and its change logs and metadata, and so is somewhat more tractable. However, a great deal of the underlying database is still buried under a wrapper.

Ultimately, it took many clever workarounds, reverse-engineering, and sleuthing, in close coordination with several customers, before Fivetran was able to build a connector that delivered reasonably complete and timely data. To this day, some tables and data elements are of unclear provenance or poorly understood, and the NetSuite connector remains something of a work in progress. The Fivetran team is still continually tuning and optimizing the NetSuite connector.

## Don’t Try This at Home

Despite offering an essential, complex, and multifaceted service, NetSuite appears to have little interest in helping its customers use its APIs easily. The challenges the Fivetran team experienced while building the NetSuite connector have stymied company after company, turning the Fivetran NetSuite connector into a staple among our frustrated customers.

If you try to build your own NetSuite connector, you will run straight into the classical trilemma or triple constraint of complex engineering projects: balancing cost, speed, and scope. At least one will have to be sacrificed. By purchasing the Fivetran NetSuite connector, you can have your cake and eat it, too — and obviate the frustration and false steps that would otherwise consume the attention of your engineers.
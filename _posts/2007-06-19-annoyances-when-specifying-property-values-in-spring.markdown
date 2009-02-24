--- 
permalink: /posts/annoyances-when-specifying-property-values-in-spring
title: Annoyances when specifying property values in Spring
tags: 
- acegi
- spring
layout: post
---
Can you spot any issues with specifying a value like this:

    <bean id="casProxyTicketValidator" 
       class="org.acegisecurity.providers.cas.ticketvalidator.CasProxyTicketValidator">
      <property name="casValidate">
        <value>https://localhost:8443/cas/proxyValidate</value>
      </property>
      <property name="proxyCallbackUrl">
        <value>
          http://localhost:8085/cbip/casProxy/receptor
        </value>
      </property>
      <property name="serviceProperties">
        <ref bean="serviceProperties" />
      </property>
    </bean>

See the value of `proxyCallbackUrl`? Because of the way it's spaced, it ends up getting newlines and spaces in the value. This issue caused me quite a headache today.

One way to address would be to make sure `<value>` immediately surrounds the actual value. But there's actually a cleaner syntax for doing this. It also has the benefit of being more succinct. Here's what it looks like after being fixed:

    <bean id="casProxyTicketValidator"
         class="org.acegisecurity.providers.cas.ticketvalidator.CasProxyTicketValidator">
      <property name="casValidate" value="https://localhost:8443/cas/proxyValidate"/>
      <property name="proxyCallbackUrl" value="http://localhost:8085/cbip/casProxy/receptor"/>
      <property name="serviceProperties" ref="serviceProperties"/>
    </bean>`

Much better, right?
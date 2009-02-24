--- 
permalink: /posts/tracking-down-oracle-constraint-violations-with-a-little-sql-and-toad.html
title: Tracking down Oracle constraint violations with a little SQL and Toad
tags: 
- oracle
- sql
- toad
layout: post
---
I really hate stack traces that look like:

    Could not execute JDBC batch update; nested exception is org.hibernate.exception.ConstraintViolationException: Could not execute JDBC batch update
    
    org.springframework.dao.DataIntegrityViolationException: Could not execute JDBC batch update; nested exception is org.hibernate.exception.ConstraintViolationException: Could not execute JDBC batch update
    Caused by: org.hibernate.exception.ConstraintViolationException: Could not execute JDBC batch update
    at org.hibernate.exception.SQLStateConverter.convert(SQLStateConverter.java:71)
    at org.hibernate.exception.JDBCExceptionHelper.convert(JDBCExceptionHelper.java:43)
    at org.hibernate.jdbc.AbstractBatcher.executeBatch(AbstractBatcher.java:249)
    at org.hibernate.jdbc.AbstractBatcher.prepareStatement(AbstractBatcher.java:92)
    at org.hibernate.jdbc.AbstractBatcher.prepareStatement(AbstractBatcher.java:87)
    at org.hibernate.jdbc.AbstractBatcher.prepareBatchStatement(AbstractBatcher.java:218)
    at org.hibernate.persister.entity.AbstractEntityPersister.insert(AbstractEntityPersister.java:2220)
    at org.hibernate.persister.entity.AbstractEntityPersister.insert(AbstractEntityPersister.java:2656)
    at org.hibernate.action.EntityInsertAction.execute(EntityInsertAction.java:52)
    at org.hibernate.engine.ActionQueue.execute(ActionQueue.java:248)
    at org.hibernate.engine.ActionQueue.executeActions(ActionQueue.java:232)
    at org.hibernate.engine.ActionQueue.executeActions(ActionQueue.java:139)
    at org.hibernate.event.def.AbstractFlushingEventListener.performExecutions(AbstractFlushingEventListener.java:298)
    at org.hibernate.event.def.DefaultFlushEventListener.onFlush(DefaultFlushEventListener.java:27)
    at org.hibernate.impl.SessionImpl.flush(SessionImpl.java:1000)
    at org.springframework.orm.hibernate3.HibernateTemplate$27.doInHibernate(HibernateTemplate.java:806)
    at org.springframework.orm.hibernate3.HibernateTemplate.execute(HibernateTemplate.java:367)
    at org.springframework.orm.hibernate3.HibernateTemplate.flush(HibernateTemplate.java:804)
    at edu.mit.broad.cbip.service.lims.TestAliquotContainerService.testCreateWellFromLiquidTransferWithTargetPosition(TestAliquotContainerService.java:290)
    Caused by: java.sql.BatchUpdateException: ORA-00001: unique constraint (JNICHOLS.SYS_C003345131) violated

at oracle.jdbc.driver.DatabaseError.throwBatchUpdateException(DatabaseError.java:602)
at oracle.jdbc.driver.OraclePreparedStatement.executeBatch(OraclePreparedStatement.java:9350)
at oracle.jdbc.driver.OracleStatementWrapper.executeBatch(OracleStatementWrapper.java:210)
at com.mchange.v2.c3p0.impl.NewProxyPreparedStatement.executeBatch(NewProxyPreparedStatement.java:1723)
at org.hibernate.jdbc.BatchingBatcher.doExecuteBatch(BatchingBatcher.java:48)
at org.hibernate.jdbc.AbstractBatcher.executeBatch(AbstractBatcher.java:242)

It doesn't really tell me much about what's happening. The key here is to look at:

    Caused by: java.sql.BatchUpdateException: ORA-00001: unique constraint (JNICHOLS.SYS_C003345131) violated

A unique constraint, how fun! `JNICHOLS` is the schema I'm using, and `SYS_C003345131` is the constraint.

After some tips from my team members, I have a reasonable way to track it down using [Toad](http://www.toadsoft.com/). It's not so much open source, but there is a free version. We actually have a site license for the commercial version, so that's what I'm going with, but I'm pretty sure the free version will work for this as well.

_Note: I don't have a good way of capturing screen shots, so bear with me._

So, the first step is to open up Toad, and then open an 'Editor' window (under 'Database -> Editor'). Use the following SQL, substituting the constraint you had.

<pre><code class="sql">
select table_name from user_constraints where constraint_name = 'SYS_C003345131';
</code></pre>

Now we know the offending table. In my case, it's ALIQUOTCONTAINER.

Open up a 'Scheme Browser' (under Database -> Schema Browser'). Here you will see a pane on the left containing a list of tables. Select the offending table from this list.

In the right pane, you will see several tables. Select the 'Constraints' tab. now you will see a list of constraints for the table.

From here, you can scan by eye for your offending constraint (I usually just go by last few digits). Of course, this is the easy part... now you have to figure out how it got into that state :)

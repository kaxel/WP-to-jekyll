---
layout: blog
title: Inexpensive MAX queries in PL SQL
permalink: 2007/12/inexpensive-max-queries-in-pl-sql
categories: [Code]
---

<p>This was a trick that one of our contractors showed me here at Monstertrak today. Basically, here&#039;s the gist. you&#039;ve got a join that you need to make, but you only ever want one record to return - you need to restrict the join to return one - and only one - sister record.<br />
Common logic would have you doing a group by query against said table as a derived table, on the primary key and whatever other field encompasses the info you need to enforce your logic, and doing a self-join against that same table to pull in the rest of the fields you need, and then joining that back against the table you needed to join to in the first place.</p>
<p>Here&#039;s what that looks like -</p>
<p>SELECT MAIN_TABLE.*, JOIN_TABLE.*<br />
FROM MAIN_TABLE<br />
     ,JOIN_TABLE<br />
     ,(SELECT MAX(ID) ID, USERNAME FROM JOIN_TABLE GROUP BY ID, USERNAME) JOIN_TABLE_GROUP_BY<br />
WHERE MAIN_TABLE.ID = JOIN_TABLE.FOREIGN_KEY<br />
AND JOIN_TABLE.ID = JOIN_TABLE_GROUP_BY.ID</p>
<p>There is a better way. You can save yourself the self-join by concatenating all of your needed fields into one calculated field, and doing the MAX() calculation with only ONE field in the GROUP BY. You put your criteria field far left, pad it enough to not chop any digits, concatenate in the rest of what you want, and then SUBSTR it back out in the end. Genius.</p>
<p>select c.id,<br />
c.email,<br />
trim(substr(v.val,13,10)) eaid,<br />
trim(substr(v.val,23)) username<br />
from MAIN_TABLE c,<br />
(select max(rpad(company_id,12)||rpad(id,10)||username) val from JOIN_TABLE ea<br />
where ea.LAST_LOGIN &gt; &#039;1-JAN-2006&#039;<br />
group by ea.company_id) v<br />
where c.id = trim(substr(v.val,1,12))<br />
and length(c.email) &gt; 3<br />
order by c.email asc, c.id desc;</p>
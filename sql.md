---
layout: page
title: SQL
permalink: /sql/
---

quick notes from a program I'm working on to track expenses


summarize the payments per employee:
{% highlight sql %}
select laborers.last_name as last,
       sum(labor_payments.amount) as total
    from (income.labor_payments inner join income.laborers on
       labor_payments.payee = laborers.id)
    group by last_name
    order by total desc;
{% endhighlight %}

 Get payments by employee from db:
{% highlight sql %}
 SELECT
   labor_payments.amount,
   labor_payments.check_number,
   labor_payments.date,
   laborers.first_name,
   laborers.last_name
 FROM
   income.labor_payments,
   income.laborers
 WHERE
   labor_payments.payee = laborers.id
 ORDER BY last_name;
{% endhighlight %}

income schema
{% highlight sql %}
CREATE TABLE labor_payments (
    id integer NOT NULL,
    payee integer,
    date date,
    amount money,
    check_number integer
);

CREATE TABLE laborers (
    first_name character varying(32),
    last_name character varying(32),
    id integer NOT NULL
);
{% endhighlight %}	    


And from another program (using web2py's default auth schema), to determine last login time per account.

{% highlight sql %}
select u.last_name, u.first_name, max(e.time_stamp) as ts
from auth_user as u inner join auth_event as e
on u.id = e.user_id
group by u.id
order by ts;
{% endhighlight %}

Unrelated. My first plpgsql function, just return 42.

{% highlight sql %}

CREATE OR REPLACE FUNCTION fortytwo()
  RETURNS integer AS
  $BODY$
  declare
	quantity integer := 42;
  begin	 
  	 raise notice 'Quantity is %', quantity; -- prints 42
         return quantity;
  end;
  $BODY$
LANGUAGE plpgsql VOLATILE
  COST 100;
{% endhighlight %}

# Quarterly unemployment report
{% highlight sql %}
select first_name, last_name, sum(state_ui)
from employees.paychecks
join employees.employees on (paychecks.emp_id = employees.emp_id)
where paydate >= '2016-01-01'::date
and paydate <= '2016-04-01'::date
group by employees.emp_id
order by sum(state_ui) desc;
{% endhighlight %}
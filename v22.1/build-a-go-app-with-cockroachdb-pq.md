---
title: Build a Go App with CockroachDB the Go pq Driver
summary: Learn how to use CockroachDB from a simple Go application with the Go pq driver.
toc: true
twitter: false
referral_id: docs_go_pq
filter_category: crud_go
filter_html: Use <strong>lib/pq</strong></button>
filter_sort: 3
docs_area: get_started
---

{% include filter-tabs.md %}

This tutorial shows you how build a simple Go application with CockroachDB and the Go [pq driver](https://github.com/lib/pq).

## Step 1. Start CockroachDB

{% include {{page.version.version}}/app/start-cockroachdb.md %}

## Step 2. Create a database

{% include {{page.version.version}}/app/create-a-database.md %}

## Step 3. Run the Go code

You can now run the code sample (`main.go`) provided in this tutorial to do the following:

- Create a table in the `bank` database.
- Insert some rows into the table you created.
- Read values from the table.
- Execute a batch of statements as an atomic [transaction](transactions.html).

    Note that CockroachDB may require the [client to retry a transaction](transactions.html#transaction-retries) in the case of read/write contention. The [CockroachDB Go client](https://github.com/cockroachdb/cockroach-go) includes a generic **retry function** (`ExecuteTx()`) that runs inside a transaction and retries it as needed. The code sample shows how you can use this function to wrap SQL statements.

### Get the code

You can copy the code below, [download the code directly](https://raw.githubusercontent.com/cockroachlabs/hello-world-go-pq/master/main.go), or clone [the code's GitHub repository](https://github.com/cockroachlabs/hello-world-go-pq).

Here are the contents of `main.go`:

{% include_cached copy-clipboard.html %}
~~~ go
{% remote_include https://raw.githubusercontent.com/cockroachlabs/hello-world-go-pq/master/main.go %}
~~~

### Update the connection parameters

<section class="filter-content" markdown="1" data-scope="local">

Edit the connection string passed to `sql.Open()` so that:

- `{username}` and `{password}` specify the SQL username and password that you created earlier.
- `{hostname}` and `{port}` specify the hostname and port in the `(sql)` connection string from SQL shell welcome text.

</section>

<section class="filter-content" markdown="1" data-scope="cockroachcloud">

Replace the string passed to `sql.Open()` with the connection string that you copied [earlier](#set-up-your-cluster-connection) from the **Connection info** dialog.

The function call should look similar to the following:

{% include_cached copy-clipboard.html %}
~~~ go
db, err := sql.Open("postgres", "postgresql://{user}:{password}@{globalhost}:26257/bank?sslmode=verify-full&sslrootcert={path to the CA certificate}&options=--cluster={cluster_name}")
~~~

{% include {{page.version.version}}/app/cc-free-tier-params.md %}

</section>

### Run the code

Initialize the module:

{% include_cached copy-clipboard.html %}
~~~ shell
$ go mod init basic-sample
~~~

Then run the code:

{% include_cached copy-clipboard.html %}
~~~ shell
$ go run main.go
~~~

The output should be:

~~~
Balances:
1 1000
2 250
Success
Balances:
1 900
2 350
~~~

To verify that the SQL statements were executed, run the following query from inside the SQL shell:

{% include_cached copy-clipboard.html %}
~~~ sql
> USE bank;
~~~

{% include_cached copy-clipboard.html %}
~~~ sql
> SELECT id, balance FROM accounts;
~~~

The output should be:

~~~
  id | balance
-----+----------
   1 |     900
   2 |     350
(2 rows)
~~~

## What's next?

Read more about using the [Go pq driver](https://godoc.org/github.com/lib/pq).

{% include {{ page.version.version }}/app/see-also-links.md %}
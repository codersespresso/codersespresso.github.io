---
layout: post
title:  "Python & SQLite: Getting started"
date:   2019-03-03 19:27:22 -0800
categories: jekyll update
---
In this post, we'll see an example on how to perform the CRUD operations (create, read, update and delete) on a SQLite database using Python 3's `sqlite` module.

**SQLite**

SQLite is a C library that provides a lightweight disk-based embeddable database that doesn’t require a separate server process and allows accessing the database using a variant of the SQL query language. [More about SQLite and Python's `sqlite3` module] [python3-sqlite-doc]

**Libraries, Frameworks, Tools**

* Python 3 (3.6.7 to be specific)
* `sqlite3` module (part of the Python's standard library)
* `pylint` (source-code, bug and quality checker for the Python programming language)
* Any text editor (Visual Studio Code, Pycharm, etc.)

**Example code**

{% highlight python %}
#!/usr/bin/python3

import sqlite3
import uuid

TEST_DATABASE_SQLITE = 'testdatabase.sqlite'


def drop_books_table(connection):
    cursor = connection.cursor()
    cursor.execute('DROP TABLE IF EXISTS books')
    connection.commit()


def create_books_table(connection):
    cursor = connection.cursor()

    query = '''
        CREATE TABLE IF NOT EXISTS books(
            id TEXT PRIMARY KEY,
            name TEXT NOT NULL,
            language TEXT NOT NULL,
            publisher TEXT NOT NULL
        )
    '''

    cursor.execute(query)
    connection.commit()


def add_some_books(connection):
    cursor = connection.cursor()
    query = 'INSERT INTO books (id, name, language, publisher) VALUES (?, ?, ?, ?)'
    cursor.execute(query, (str(uuid.uuid4()), 'Learning Python', 'English', 'Oreilly Media'))
    cursor.execute(query, (str(uuid.uuid4()), 'Effective Java', 'English', 'Addison-Wesley Professional'))
    connection.commit()


def read_books(connection):
    cursor = connection.cursor()
    cursor.execute('SELECT id, name, language, publisher FROM books')
    
    for book in cursor.fetchall():
        print(book)


def update_book(connection):
    cursor = connection.cursor()
    cursor.execute('UPDATE books SET name = ? WHERE name = ?', ('Learning Python 5th Edition', 'Learning Python'))
    connection.commit()


def delete_book(connection):
    cursor = connection.cursor()
    cursor.execute('DELETE FROM books WHERE name = ?', ('Effective Java',))
    connection.commit()


def main():
    connection = sqlite3.connect(TEST_DATABASE_SQLITE)

    # ddl statements
    drop_books_table(connection)
    create_books_table(connection)

    # dml statements
    add_some_books(connection)  # create
    print("after adding new books")
    read_books(connection)  # read

    update_book(connection)  # update
    print("after updating a book")
    read_books(connection)

    delete_book(connection)  # delete
    print("after deleting a book")
    read_books(connection)

    connection.close()


if __name__ == "__main__":
    main()
{% endhighlight %}

[python3-sqlite-doc]: https://docs.python.org/3/library/sqlite3.html

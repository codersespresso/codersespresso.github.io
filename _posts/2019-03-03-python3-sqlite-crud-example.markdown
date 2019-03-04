---
layout: post
title:  "Python & SQLite: Getting started"
date:   2019-03-03 19:27:22 -0800
categories: jekyll update
---
In this post, we'll see an example for how to perform the CRUD operations (create, read, update and delete) in a SQLite database using Python 3.

**SQLite database**

SQLite is a C library that provides a lightweight disk-based embeddable database that doesn’t require a separate server process and allows accessing the database using a variant of the SQL query language. [sqlite3] [python3-sqlite-doc]

**Tools used**

* Python 3 (specifically, 3.6.7)
* sqlite3 module (part of the Python's standard library)
* pylint (source-code, bug and quality checker for the Python programming language)
* Visual Studio Code (a text editor)

**Example code**

{% highlight python %}
#!/usr/bin/python3

import sqlite3
import uuid


def drop_books_table():
    connection = sqlite3.connect('testdatabase.sqlite')
    cursor = connection.cursor()
    query = 'DROP TABLE IF EXISTS books'
    cursor.execute(query)
    connection.commit()
    connection.close()


def create_books_table():
    connection = sqlite3.connect('testdatabase.sqlite')
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
    connection.close()


def add_some_books():
    connection = sqlite3.connect('testdatabase.sqlite')
    cursor = connection.cursor()
    query = 'INSERT INTO books (id, name, language, publisher) VALUES (?, ?, ?, ?)'
    cursor.execute(query, (str(uuid.uuid4()), 'Learning Python', 'English', 'Oreilly Media'))
    cursor.execute(query, (str(uuid.uuid4()), 'Effective Java', 'English', 'Addison-Wesley Professional'))
    connection.commit()
    connection.close()


def read_books():
    connection = sqlite3.connect('testdatabase.sqlite')
    cursor = connection.cursor()
    cursor.execute('SELECT id, name, language, publisher FROM books')

    for book in cursor.fetchall():
        print(book)

    connection.close()


def update_book():
    connection = sqlite3.connect('testdatabase.sqlite')
    cursor = connection.cursor()
    cursor.execute('UPDATE books SET name = ? WHERE name = ?', ('Learning Python 5th Edition', 'Learning Python'))
    connection.commit()
    connection.close()


def delete_book():
    connection = sqlite3.connect('testdatabase.sqlite')
    cursor = connection.cursor()
    cursor.execute('DELETE FROM books WHERE name = ?', ('Effective Java',))
    connection.commit()
    connection.close()


def main():
    print("creating books table; re-creating if it already exists")
    drop_books_table()
    create_books_table()

    add_some_books() # Create
    print("after adding new books")
    read_books() # Read

    update_book() # Update
    print("after updating a book")
    read_books()

    delete_book() # Delete
    print("after deleting a book")
    read_books()


if __name__ == "__main__":
    main()

{% endhighlight %}

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
[python3-sqlite-doc]: https://docs.python.org/3/library/sqlite3.html

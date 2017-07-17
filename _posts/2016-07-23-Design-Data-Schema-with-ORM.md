---
layout: post
title: Design Data Schema with ORM
category: python web
date: 2016-07-23 15:13:31
---

In most web applications nowadays, data is stored and retrieved from a Relational Database Management System(RDBMS), which is a database that hold data in a tabular format with rows and columns and is able to compare data across tables, including MySQL, Postgres, Oracle, and MSSQL and so on. And Object Relational Mapping(ORM) make it friendly to interactive with different database. An ORM is a technique to pass and convert data between two sources with different types of systems and data structures. Also, a programming language such as Python allows you to have different objects that hold references to each other, and get and set their attributes.

A database framework, such as SQLAlchemy and Django built-in ORM framework, is a database API at its lowest level and performs ORM at its highest level. There are lots of factors to evaluate when choosing a database framework.

1. Ease of use

    When comparing straight database engines versus database abstraction layers, the second group clearly wins. Abstraction layers, also called object-relational mappers(ORMs), provide transparent conversion of high-level object-oriented operations into low-level database instructions.

2. Performance

    The conversions that ORMs have to do to translate from the object domain into the database domain have an overhead. In most cases, the performance penalty is negligible, but they may not always be. In general, the productivity gain obtained with ORMs far outweighs a minimal performance degradation, so this isn't a valid argument to drop ORMs completely. What make sense is to choose a database abstraction layer that provides optional access to the underlying database in case specific operatins need to be optimized by implementing them directly as native database instructions.

3. Portability

   The database choices available on your development and production platforms must be considered. For example, if you plan to host your application on a cloud platform, then you should find out what database choices this service offers. Another portability aspece applies to ORMs. Although some of these frameworks provide an abstraction layer for a single database engine, others abstract even higher and provide a choice of database engines---all accessible with the same object-oriented interface. The best example of this is the SQLAlchemy ORM, which supports a list of relatinoal database engines including the popular MySQL, Postgres, and SQLite.

Now we show how to create database schema in Flask with SQLAlchemy and Django with its build-in ORM framework. An One-to-One relationship, for example, a person has a name, can be written like this in Flask-SQLAlchemy:

```python
class User(db.Model):
    ...
    username = db.Column(db.String(80), unique=True)
    ...
```

And like this in Django:

```python
class User(models.Model):
    ...
    username = models.CharField(max_length=250)
    ...
```
An One-to-Many relationship, for example, a post has many comments, can be written like his in Flask-SQLAlchemy:

```python
class Post(db.Model):
    ...
    comments = db.relationship('Comment', backref='post',
                                lazy='dynamic')
    ...

class Comment(db.Model):
    ...
    post = db.Column(db.Integer, db.ForeignKey('post.id'))
    ...
    
```
And like this in Django:

```python
class Comment(models.Model):
    ...
    post = models.ForeignKey(Post, related_name='comments')
    ...
```
An Many-to-Many relationship, for example, posts with tags, can written like this in Flask-SQLAlchemy:

```python
tags = db.Table('tags',
    db.Column('tag_id', db.Integer, db.ForeignKey('tag.id')),
    db.Column('post_id', db.Integer, db.ForeignKey('post.id'))
)

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    tags = db.relationship('Tag', secondary=tags,
        backref=db.backref('posts', lazy='dynamic'))

class Tag(db.Model):
    id = db.Column(db.Integer, primary_key=True)
```
And writtend like this in Django:

```python
from django.db import models

class Tag(models.Model):
    title = models.CharField(max_length=30)

    def __str__(self):            
        return self.title

    class Meta:
        ordering = ('title',)

class Post(models.Model):
    ...
    tags = models.ManyToManyField(Tag)
    ...
```


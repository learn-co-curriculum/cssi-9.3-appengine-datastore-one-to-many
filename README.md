---
tags: appengine, datastore, database, relationships, python
level: 4
languages: python
---

# App Engine One-to-Many Relationships

## Objectives

+ Understand what a One-to-Many relationship symbolizes
+ Can implement such a relationship with App Engine's Datastore

## Motivation / Why Should You Care?

Often we want to represent real world concepts in code. For example we might want to store all of the posts a user has made on Facebook in our database. In this case *One* user has *Many* associated posts, but a post was only ever created by one user. This is a foundational component of database technology that allows for complex behaviors.

## Lesson

### One-to-Many Models

You can easily model the one to many relationship using `KeyProperty`, which allows us to store the key of another database model.

Let's talk about the NBA. How many players can be on one team? A lot right? But how many teams can a player belong to? Only one. So one team can have many players. Many players can belong to one (and only one) team.  So we need to set up a one-to-many relationship.

```python
import os
import webapp2
from google.appengine.ext import ndb

class MainHandler(webapp2.RequestHandler):
    def get(self):

        class Team(ndb.Model):
            city = ndb.StringProperty()
            name = ndb.StringProperty()

        class Player(ndb.Model):
            team = ndb.KeyProperty(kind='Team')
            last_name= ndb.StringProperty()
            position = ndb.StringProperty()
```

Notice that in this example, we define team as a KeyProperty. This means we are going to link the player’s team with our Team model.  Notice that reference property takes two arguments. The first is the model we want to link to: Team. This means we can only assign values of the type Team to the “team” variable. The next argument is what we want to call the collection, which in this case is roster.

Now we’re ready to add many players to our team, the Bulls.

```python
da_bulls= Team(city='Chicago', name='Bulls')
bulls_key = da_bulls.put()

drose = Player(team=bulls_key,last_name='Rose', position='Point Guard')
joakim = Player(team=bulls_key,last_name='Noah', position='Center')

drose.put()
joakim.put()
```

We can use the datastore viewer in our admin port to check if these entries were created. First look at the AppEngine Launcher to find your admin port. Then put in localhost:NNNN where the 4 N’s are replaced by your port number (for most of you, it should be 8000). Then click Datastore Viewer.

To show all of the players on Chicago’s roster, we  need to make a query of all of our players on the Bulls, store that data into a dictionary using .fetch, and then loop through that dictionary.

```python
player_query = Player.query(Player.team = bulls_key)
player_data = player_query.fetch()
for player in player_data:
     self.response.out.write('<p>'+player.last_name+'</p>')
```



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
from google.appengine.ext import ndb

class Team(ndb.Model):
    city = ndb.StringProperty()
    name = ndb.StringProperty()
    players = ndb.KeyProperty(Player, repeated=True)

class Player(ndb.Model):
    last_name= ndb.StringProperty()
    position = ndb.StringProperty()
```

Notice that in this example, we define players as a KeyProperty. This means we are going to link a team's players with the Player Model

Now we’re ready to add many players.

```python
drose = Player(last_name='Rose', position='Point Guard')
drose.put()
joakim = Player(last_name='Noah', position='Center')
joakim.put()
```

We can use the datastore viewer in our admin port to check if these entries were created. First look at the AppEngine Launcher to find your admin port. Then put in localhost:NNNN where the 4 N’s are replaced by your port number (for most of you, it should be 8000). Then click Datastore Viewer.
 
Now that we have our players, we need to add them to our team. Since the players property in the Team model has an attribute, `repeated=True`, it is expecting a list. To initalize our team, we can just include our drose entitiy that we have already created. To access that entity and all of it's information we need to use drose.key
 
 If we wanted to add a new player to the team, we could use the append method.
```python

da_bulls= Team(city='Chicago', name='Bulls', players=[drose.key])
da_bulls.players.append(joakim.key)
da_bulls.put()

```
 



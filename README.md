# SQL CRUD

# Part 1: Restaurant Finder
## Creating Restaurant Table
```sql
CREATE TABLE restaurants(
ID INTEGER PRIMARY KEY,
name TEXT NOT NULL,
category TEXT NOT NULL,
price_tier TEXT NOT NULL,
neighborhood TEXT NOT NULL,
open_hour TIME NOT NULL,
close_hour TIME NOT NULL,
avg_rating INTEGER NOT NULL,
forKids BOOLEAN NOT NULL);
```

## Creating Reviews Table

```sql
CREATE TABLE reviews(
ID INTEGER PRIMARY KEY,
restaurant_ID INTEGER NOT NULL,
review TEXT NOT NULL);
```

## Importing Data
The original data is contained in the [restaurants_data.csv](data/restaurants_data.csv) file and was generated using mockaroo.
The following commands were used to import data in the restaurants table:
```sql
.mode csv
.headers on
.import --csv --skip 1 'data/restaurants_data.csv' restaurants
```

## SQL Queries
1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
```sql
SELECT * FROM restaurants WHERE price_tier="cheap" AND neighborhood="Harlem";
```

2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.
```sql
SELECT * FROM restaurants WHERE category="Pakistani" AND avg_rating>2 ORDER BY avg_rating DESC;
```

3. Find all restaurants that are open now (see hint below).
```sql
SELECT * from restaurants where strftime('%H', 'now') > strftime('%H', open_hour) and strftime('%H', 'now') < strftime('%H', close_hour);
```

4. Leave a review for a restaurant (pick any restaurant as an example).
```sql
INSERT INTO reviews (restaurant_ID,review) VALUES (988,"Undercooked Steaks, bland taste, does not live up to the ratings");
```

5. Delete all restaurants that are not good for kids.
```sql
DELETE FROM restaurants WHERE forKids=false;
```

6. Find the number of restaurants in each NYC neighborhood.
```sql
SELECT neighborhood, count(ID) AS number_restaurants FROM restaurants GROUP BY neighborhood;
```

<br>

# Part 2: Social Media App
## Creating Tables
### Users Table
```sql
CREATE TABLE users(
ID INTEGER PRIMARY KEY,
email TEXT NOT NULL,
password TEXT NOT NULL,
handle TEXT NOT NULL);
```

### Posts Table
```sql
CREATE TABLE posts(
ID INTEGER PRIMARY KEY,
user_ID INTEGER NOT NULL,
content TEXT NOT NULL,
post_type TEXT NOT NULL,
recepient_ID INTEGER DEFAULT NULL,
visibility BOOLEAN DEFAULT TRUE,
post_time DATETIME DEFAULT CURRENT_TIMESTAMP);
```

## Importing Data
The original data for users is contained in the [users_data.csv](data/users_data.csv) file and the original data for posts is contained in the [posts_data.csv](data/posts_data.csv) and was generated using mockaroo.

The following commands were used to import data in the respective tables of the database:
```sql
.mode csv
.headers on
.import --csv --skip 1 'data/users_data.csv' users
.import --csv --skip 1 'data/posts_data.csv' posts
```

Furthermore, the following command was run to ensure consistency of format between values of different records after importing the csv file:
```sql
UPDATE posts SET recepient_ID=NULL where recepient_ID="";
```

## SQL Queries
1. Register a new User.
```sql
INSERT INTO users (email,password,handle) VALUES ("fake@nodomain.com","veryweakpassword","fakeAccount");
```

2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).
```sql
INSERT INTO posts (user_ID,content,post_type,recepient_ID) VALUES (926,"I am sending a test message","message",276);
```

3. Create a new Story by a particular User (pick any User for example).
```sql
INSERT INTO posts (user_ID,content,post_type) VALUES (679,"My test story is uploading","story");
```

4. Show the 10 most recent visible Messages and Stories, in order of recency.
```sql
SELECT * FROM posts WHERE visibility=true ORDER BY post_time DESC LIMIT 10;
```

5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
```sql
SELECT * FROM posts WHERE post_type="message" AND user_ID=472 AND recepient_ID=557 AND visibility=true ORDER BY post_time DESC LIMIT 10;
```

6. Make all Stories that are more than 24 hours old invisible.
```sql
UPDATE posts SET visibility=false WHERE ROUND((JULIANDAY('now') - JULIANDAY(post_time)) * 24) > 24 AND post_type="story";
```

7. Show all invisible Messages and Stories, in order of recency.
```sql
SELECT * FROM posts WHERE visibility=false ORDER BY post_time DESC;
```

8. Show the number of posts by each User.
```sql
SELECT user_ID,COUNT(user_ID) AS num_posts FROM posts GROUP BY user_ID; 
```

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.
```sql
SELECT email, content FROM users INNER JOIN posts ON users.ID=posts.user_ID WHERE ROUND((JULIANDAY('now') - JULIANDAY(post_time)) * 24) <= 24;
```

10. Show the email addresses of all Users who have not posted anything yet.
```sql
SELECT email FROM users LEFT JOIN posts on users.ID=posts.user_ID where content is NULL;
```




# SQL CRUD Report
## Part 1: Restaurant Finder
### Table Structure
Generate the data [retaurants.csv](data/restaurants.csv) throuh the website: [Mockaroo](https://mockaroo.com/)
```sql

CREATE TABLE restaurants (
    id INTEGER PRIMARY KEY,
    category TEXT,
    price_tier TEXT,
    neighborhood TEXT,
    opening_hours TEXT,
    average_rating REAL,
    good_for_kids BOOLEAN
);


CREATE TABLE reviews (
    id INTEGER PRIMARY KEY,
    id INTEGER,
    rating INTEGER,
    review_text TEXT,
    date_posted TEXT,
    FOREIGN KEY (id) REFERENCES restaurants(id)
);

```


### Practice Data
Import the data into the restaurants table from a CSV file:

```sql
.mode csv
.headers on
.import ./data/restaurants.csv
```

### Queries
1.Find the cheap restaurants in a particular NYC neighborhood
```sql
SELECT * FROM restaurants
WHERE price_tier = 'Cheap' AND neighborhood = 'Queens';
```

2.Find restaurants by Genre with High Rating
```sql
SELECT * FROM restaurants
WHERE category = 'Japanese' AND average_rating >= 3
ORDER BY average_rating DESC;
```

3.Find all restaurants that are open now
```sql
SELECT *
FROM restaurants
WHERE substr(opening_hours, 1, 5) <= strftime('%H:%M', 'now', 'localtime') 
AND substr(opening_hours, 7, 5) >= strftime('%H:%M', 'now', 'localtime');
```

4.Leave a review for a restaurant
```sql
INSERT INTO reviews (restaurant_id, rating, review_text, date_posted)
VALUES (1, 5, 'Outstanding experience!', '2024-02-28');
```

5.Delete all restaurants that are not good for kids.
```sql
DELETE FROM restaurants
WHERE good_for_kids = false;

```

6.Find the number of restaurants in each NYC neighborhood
```sql
SELECT neighborhood, COUNT(*) AS num_restaurants
FROM restaurants
GROUP BY neighborhood;

```

## Part 2: Social Media App

### Tables Structure
Create 2 Using [Mockaroo](https://mockaroo.com/) to make tables for this app [users.csv](data/users.csv) and [posts.csv](data/posts.csv)
.

```sql

CREATE TABLE users (
    user_id INTEGER PRIMARY KEY,
    email TEXT NOT NULL,
    password TEXT NOT NULL,
    handle TEXT NOT NULL
);


CREATE TABLE posts (
    post_id INTEGER PRIMARY KEY,
    user_id INTEGER,
    content TEXT,
    post_type TEXT CHECK(post_type IN ('Message', 'Story')),
    recipent_id INTEGER,
    visible BOOLEAN DEFAULT TRUE,
    post_timestamps TEXT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### Practice Data
```sql
.mode csv
.headers on
.import ./data/users.csv users
.import ./data/posts.csv posts
```

### Queries
1.Register a new User
```sql
INSERT INTO users (email, password, handle)
VALUES ('zyhsdsg@example.com', 'securepassword', 'newuserhandle');

```
2.Create a new Message sent by a particular User to a particular User
```sql
INSERT INTO posts (user_id, content, post_type, visible)
VALUES (1, 'Hello from user 1 to user 2!', 'Message', TRUE);
```

3.Create a new Story by a particular User
```sql
INSERT INTO posts (user_id, content, post_type, recipient_id,visible)
VALUES (1, 'Check out my new story!', 'Story', TRUE);
```

4.Show the 10 most recent visible Messages and Stories, in order of recency
```sql
SELECT *
FROM posts
WHERE visible = true
ORDER BY timestamp DESC
LIMIT 10;
```

5.Show the 10 most recent visible Messages sent by a particular User to another User
```sql
SELECT *
FROM posts
WHERE user_id = 1 AND post_type = 'message' AND visible = TRUE
ORDER BY timestamp DESC
LIMIT 10;
```


6.Make all Stories that are more than 24 hours old invisible
```sql
UPDATE posts
SET visible = false
WHERE post_type = 'Story' 
AND (JULIANDAY('now') - JULIANDAY(post_timestamp)) * 24 >= 24;
```

7.Show all invisible Messages and Stories, in order of recency
```sql
SELECT *
FROM posts
WHERE visible = false
ORDER BY timestamp DESC;
```

8.Show the number of posts by each User
```sql
SELECT users.id, users.handel COUNT(posts.id) AS num_posts
FROM posts
LEFT JOIN posts ON users.id = posts.user_id
GROUP BY user.id, users.handel
```


9.Show the post text and email address of all posts and the User who made them within the last 24 hours
```sql
SELECT posts.content, users.email
FROM posts
JOIN users ON posts.user_id = users.id
WHERE (JULIANDAY('now') - JULIANDAY(post_timestamp)) * 24 <= 24;
```


10.Show the email addresses of all Users who have not posted anything yet
```sql
SELECT email
FROM users
WHERE user_id NOT IN (SELECT DISTINCT user_id FROM posts);
```



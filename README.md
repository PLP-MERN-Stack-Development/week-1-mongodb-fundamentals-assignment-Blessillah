[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=19902999&assignment_repo_type=AssignmentRepo)
# MongoDB Fundamentals Assignment

This assignment focuses on learning MongoDB fundamentals including setup, CRUD operations, advanced queries, aggregation pipelines, and indexing.

## Assignment Overview

You will:
1. Set up a MongoDB database
2. Perform basic CRUD operations
3. Write advanced queries with filtering, projection, and sorting
4. Create aggregation pipelines for data analysis
5. Implement indexing for performance optimization

## Getting Started

1. Accept the GitHub Classroom assignment invitation
2. Clone your personal repository that was created by GitHub Classroom
3. Install MongoDB locally or set up a MongoDB Atlas account
4. Run the provided `insert_books.js` script to populate your database
5. Complete the tasks in the assignment document

## Files Included

- `Week1-Assignment.md`: Detailed assignment instructions
- `insert_books.js`: Script to populate your MongoDB database with sample book data

## Requirements

- Node.js (v18 or higher)
- MongoDB (local installation or Atlas account)
- MongoDB Shell (mongosh) or MongoDB Compass

## Submission

Your work will be automatically submitted when you push to your GitHub Classroom repository. Make sure to:

1. Complete all tasks in the assignment
2. Add your `queries.js` file with all required MongoDB queries
3. Include a screenshot of your MongoDB database
4. Update the README.md with your specific setup instructions

## Resources

- [MongoDB Documentation](https://docs.mongodb.com/)
- [MongoDB University](https://university.mongodb.com/)
- [MongoDB Node.js Driver](https://mongodb.github.io/node-mongodb-native/)

This looks like a comprehensive and practical assignment to get hands-on with MongoDB\! I can certainly help you structure your `queries.js` file and guide you through the process.

Since I cannot directly interact with your local environment or GitHub Classroom, I will provide you with the code snippets and explanations you'll need to complete each task in your `queries.js` file.

-----

### Getting Started: MongoDB Setup and Data Population

Before diving into `queries.js`, ensure your MongoDB is set up and populated.

1.  **MongoDB Installation/Atlas Setup:**

      * **Local:** If you haven't already, download and install MongoDB Community Server and MongoDB Shell (mongosh) from the official MongoDB website. Follow their installation guides for your OS.
      * **MongoDB Atlas (Cloud - Recommended for ease):**
        1.  Go to [cloud.mongodb.com](https://cloud.mongodb.com/) and sign up for a free account.
        2.  Create a new project.
        3.  Build a new "Shared Cluster" (the free tier, M0). Choose your preferred cloud provider and region.
        4.  Set up a database user (e.g., `admin` with a strong password). Make sure to remember these credentials.
        5.  Configure network access: For simplicity during development, you can allow access from anywhere (`0.0.0.0/0`). For production, restrict this to specific IP addresses.
        6.  Once your cluster is ready, click "Connect" -\> "Connect with the MongoDB Shell" and copy the connection string. It will look something like: `mongosh "mongodb+srv://<username>:<password>@cluster0.abcde.mongodb.net/myDatabase"`. Replace `<username>` and `<password>` with your database user credentials.

2.  **Populating Your Database (`insert_books.js`):**

      * Open your terminal or command prompt.
      * Navigate to the directory where you cloned your repository and where `insert_books.js` is located.
      * If using **local MongoDB**, ensure your MongoDB server is running. Then, execute:
        ```bash
        mongosh --file insert_books.js
        ```
      * If using **MongoDB Atlas**, use the connection string you copied:
        ```bash
        mongosh "mongodb+srv://<username>:<password>@cluster0.abcde.mongodb.net/myDatabase" --file insert_books.js
        ```
        (Replace the connection string with yours).
      * This script will connect to your MongoDB instance, switch to the `library` database, and insert the sample book data into the `books` collection.

-----

### `queries.js` Content

Now, let's create your `queries.js` file. You will execute this file using `mongosh` in the same way you ran `insert_books.js`.

```javascript
// queries.js

// Connect to the 'library' database
// If you are running this script directly with mongosh --file,
// you might not need the explicit 'use library' if the connection string
// already specifies the database. However, it's good practice for clarity.
use library;

print("--- MongoDB Assignment Queries ---");

// --- 1. Basic CRUD Operations ---
print("\n--- 1. Basic CRUD Operations ---");

// --- Create (Insert One) ---
print("\n1.1. Inserting a new book...");
db.books.insertOne({
    title: "The Midnight Library",
    author: "Matt Haig",
    genre: "Fiction",
    publicationYear: 2020,
    pages: 304,
    price: 15.99,
    isAvailable: true,
    reviews: [
        { reviewer: "Reader A", rating: 5, comment: "Absolutely brilliant and thought-provoking!" }
    ]
});
print("New book inserted.");

// --- Read (Find All) ---
print("\n1.2. Finding all books (first 5 for brevity):");
db.books.find().limit(5).forEach(printjson);

// --- Read (Find One) ---
print("\n1.3. Finding a specific book by title:");
db.books.findOne({ title: "The Midnight Library" }).forEach(printjson);

// --- Update (Update One) ---
print("\n1.4. Updating the price of 'The Midnight Library':");
db.books.updateOne(
    { title: "The Midnight Library" },
    { $set: { price: 14.50, isAvailable: false } }
);
print("Book updated.");
db.books.findOne({ title: "The Midnight Library" }).forEach(printjson); // Verify update

// --- Delete (Delete One) ---
print("\n1.5. Deleting 'The Midnight Library':");
db.books.deleteOne({ title: "The Midnight Library" });
print("Book deleted.");
db.books.findOne({ title: "The Midnight Library" }).forEach(printjson); // Verify deletion (should be null)


// --- 2. Advanced Queries with Filtering, Projection, and Sorting ---
print("\n--- 2. Advanced Queries ---");

// --- Filtering: Find all books published after 2000 in the 'Science Fiction' genre ---
print("\n2.1. Books published after 2000 in 'Science Fiction' genre:");
db.books.find(
    {
        publicationYear: { $gt: 2000 },
        genre: "Science Fiction"
    }
).forEach(printjson);

// --- Projection: Find titles and authors of all 'Fantasy' books ---
print("\n2.2. Titles and authors of 'Fantasy' books:");
db.books.find(
    { genre: "Fantasy" },
    { title: 1, author: 1, _id: 0 } // 1 to include, 0 to exclude _id
).forEach(printjson);

// --- Sorting: Find all books, sorted by publication year (descending) and then by title (ascending) ---
print("\n2.3. All books, sorted by publication year (desc) and title (asc):");
db.books.find({})
    .sort({ publicationYear: -1, title: 1 })
    .limit(5) // Limit to 5 for readability
    .forEach(printjson);

// --- Combined: Find books with more than 400 pages, price less than $20,
//              project only title, author, pages, and sort by pages ascending. ---
print("\n2.4. Books > 400 pages, < $20, with specific fields, sorted by pages:");
db.books.find(
    {
        pages: { $gt: 400 },
        price: { $lt: 20 }
    },
    {
        title: 1,
        author: 1,
        pages: 1,
        _id: 0
    }
).sort({ pages: 1 })
.forEach(printjson);


// --- 3. Aggregation Pipelines for Data Analysis ---
print("\n--- 3. Aggregation Pipelines ---");

// --- 3.1. Count books by genre ---
print("\n3.1. Count of books by genre:");
db.books.aggregate([
    {
        $group: {
            _id: "$genre",
            count: { $sum: 1 }
        }
    },
    {
        $sort: { count: -1 }
    }
]).forEach(printjson);

// --- 3.2. Calculate average price and total pages for books published after 2000 ---
print("\n3.2. Average price and total pages for books published after 2000:");
db.books.aggregate([
    {
        $match: {
            publicationYear: { $gt: 2000 }
        }
    },
    {
        $group: {
            _id: null, // null for a single group for all matched documents
            averagePrice: { $avg: "$price" },
            totalPages: { $sum: "$pages" }
        }
    },
    {
        $project: {
            _id: 0, // Exclude the default _id field
            averagePrice: { $round: ["$averagePrice", 2] }, // Round to 2 decimal places
            totalPages: 1
        }
    }
]).forEach(printjson);

// --- 3.3. Find the top 3 authors by number of books ---
print("\n3.3. Top 3 authors by number of books:");
db.books.aggregate([
    {
        $group: {
            _id: "$author",
            bookCount: { $sum: 1 }
        }
    },
    {
        $sort: { bookCount: -1 }
    },
    {
        $limit: 3
    }
]).forEach(printjson);

// --- 3.4. List books with an average review rating of 4 or higher (if reviews exist) ---
print("\n3.4. Books with average review rating of 4 or higher:");
db.books.aggregate([
    {
        $match: {
            "reviews.0": { $exists: true } // Only include documents that have at least one review
        }
    },
    {
        $addFields: {
            averageRating: { $avg: "$reviews.rating" }
        }
    },
    {
        $match: {
            averageRating: { $gte: 4 }
        }
    },
    {
        $project: {
            title: 1,
            author: 1,
            averageRating: { $round: ["$averageRating", 2] },
            _id: 0
        }
    },
    {
        $sort: { averageRating: -1 }
    }
]).forEach(printjson);


// --- 4. Implementing Indexing for Performance Optimization ---
print("\n--- 4. Implementing Indexing ---");

// --- 4.1. Create an index on 'publicationYear' for faster range queries ---
print("\n4.1. Creating index on 'publicationYear' (ascending)...");
db.books.createIndex({ publicationYear: 1 });
print("Index created on publicationYear.");

// --- 4.2. Create a compound index on 'genre' and 'author' for specific searches ---
print("\n4.2. Creating compound index on 'genre' (ascending) and 'author' (ascending)...");
db.books.createIndex({ genre: 1, author: 1 });
print("Compound index created on genre and author.");

// --- 4.3. List all indexes on the 'books' collection ---
print("\n4.3. Listing all indexes on the 'books' collection:");
db.books.getIndexes().forEach(printjson);

// --- 4.4. Explain a query to show index usage (optional, for verification) ---
// This part is for you to run interactively in mongosh, not typically in a script,
// as it shows query plan details.
// You would run:
// db.books.find({ publicationYear: { $gt: 2000 }, genre: "Science Fiction" }).explain("executionStats")
// This will show if your created indexes are being used (look for "IXSCAN" in the plan).

print("\nIndexing operations completed.");
print("To verify index usage, you can run explain() on your queries in mongosh, e.g.:");
print("db.books.find({ publicationYear: { $gt: 2000 } }).explain('executionStats')");
print("db.books.find({ genre: 'Fantasy', author: 'J.R.R. Tolkien' }).explain('executionStats')");

print("\n--- Assignment Queries Complete ---");
```

-----

### How to Run `queries.js`

1.  **Save the code above** into a file named `queries.js` in your repository.
2.  **Open your terminal/command prompt.**
3.  **Navigate to your repository's root directory.**
4.  **Execute the script using `mongosh`:**
      * If using **local MongoDB**:
        ```bash
        mongosh --file queries.js
        ```
      * If using **MongoDB Atlas**, use your connection string (replace placeholders):
        ```bash
        mongosh "mongodb+srv://<username>:<password>@cluster0.abcde.mongodb.net/library" --file queries.js
        ```
        (Note: I've put `library` as the database in the connection string for convenience, matching the `use library;` command in the script).

You will see the output of each `print()` and `printjson()` command directly in your terminal, showing the results of your queries.

-----

### Final Submission Steps

1.  **Verify your `queries.js` file** is in your repository and contains all the required code.
2.  **Take a screenshot of your MongoDB database:**
      * If using **MongoDB Compass**: Connect to your database, navigate to the `library` database, and then the `books` collection. Take a screenshot showing the collection's documents and possibly the "Indexes" tab.
      * If using **MongoDB Atlas**: Log in to your Atlas account, go to "Database" -\> "Collections" for your cluster. Select the `library` database and `books` collection. Take a screenshot of the documents.
      * If using **mongosh**: You can run `db.books.find().limit(10)` and `db.books.getIndexes()` and take a screenshot of your terminal output.
3.  **Update your `README.md`:**
      * Add instructions on how you set up MongoDB (local or Atlas).
      * Explain how to run the `insert_books.js` and `queries.js` scripts.
      * Mention any specific details about your environment if necessary.
4.  **Push your changes to your GitHub Classroom repository.**

Good luck with your assignment\!

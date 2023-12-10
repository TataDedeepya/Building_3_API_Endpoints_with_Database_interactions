**Building three API Endpoints with Database Interaction**

In our assignment we intend to manage a library system.Our goal is to use API’s to interact with database to perform basic CRUD operations on the books.

First we installed all the node modules and packages needed to build our project and interact with the databases and the instructions regarding installations and error handling are written in their respective Readme files.

For our Library Management System I created a database which contain the {Name_of_the_book, Author, Genre, Edition}

_To seed the database with mockdata I used the method _
const seedDatabase = async () => {
const existingBooks = await Book.find();

if (existingBooks.length === 0) {
const booksToSeed = [
{ Name_of_the_book: 'Book1', Author: 'Author1',Genre: 'Genre1', Edition: 1 },
{ Name_of_the_book: 'Book2', Author: 'Author2',Genre: 'Genre2', Edition: 2 },
];

await Book.insertMany(booksToSeed);
console.log('Database seeded with initial books');
} else {
console.log('Database already has books, skipping seeding');
}
};

In our application I used RESTful API (Representational State Transfer API) to create web services that are interoperable, scalable, and stateless.Using standard HTTP methods like GET, POST, PUT I returned data in a structured format JSON.


**Endpoint 1: Retrieve All Books **
Implemented an endpoint that retrieves a list of all books in the library from the database.
app.get('/api/books', async (req, res) => {
const books = await Book.find();
res.json(books);
});

Which returns the required books present in the library and their Descriptions

If we enter a non existing book it returns “There is no such book”

app.get('/api/books/:id', async (req, res) => {
const book = await Book.findById(req.params.id);
if (!book) {
return res.status(404).json({ error: 'There is no such book' });
}
res.json(book);
});

 Similarly,

**Endpoint 2: Add a New Book **
Implemented an endpoint that allows the addition of a new book to the library. 
Endpoint: POST /api/books 
Request Body: JSON object representing the new book. 

app.post('/api/books', async (req, res) => {
const { Name_of_the_book, Author, Genre, Edition } = req.body;

try {
const existingBook = await Book.findOne({ Name_of_the_book, Author });

if (existingBook) {
return res.status(409).json({ error: 'That book already exists' });
}

const newBook = new Book({ Name_of_the_book, Author, Genre, Edition });
await newBook.save();
res.status(201).json(newBook);
} catch (error) {
res.status(400).json({ error: error.message });
}


If there is already a book with the name we are entering it will return “that book already exists” (for duplicate entries)


**Endpoint 3: Update Book Details **
Implemented an endpoint that allows updating the details of a specific book in the library. 
Endpoint: PUT /api/books/{id} 
Request Body: JSON object with updated book details.

app.put('/api/books/:id', async (req, res) => {
const { Name_of_the_book, Author, Genre, Edition } = req.body;

try {
const existingBook = await Book.findById(req.params.id);

if (!existingBook) {
return res.status(404).json({ error: 'Please enter a book which is already present to update its details' });
}

const updatedBook = await Book.findByIdAndUpdate(
req.params.id,
{ Name_of_the_book, Author, Genre, Edition },
{ new: true }
);

res.json(updatedBook);
} catch (error) {
res.status(400).json({ error: error.message });
}
If user wants to update the details of a non existent book then error handling exception is throwed and it returns “Please enter a book which is already present to update its details”

# Solidity Study Guide: Structs

## **1. Overview of Structs**

### **Key Concepts:**

-   A **struct** in Solidity allows defining custom data types.
-   Structs **group multiple variables** into a single entity.
-   Structs can be stored in **arrays**, **mappings**, or as **individual variables**.
-   Storage location matters: `memory` (temporary) vs. `storage` (persistent).

---

## **2. Example 1: Basic Struct Usage**

### **Key Takeaways:**

-   `struct Book` contains `title`, `author`, and `completed` fields.
-   Books are stored in an **array of structs**.
-   Different methods exist for adding struct instances.

### **Contract Code:**

```solidity
contract Structs1 {
    struct Book {
        string title;
        string author;
        bool completed;
    }

    Book[] public books;

    function add1(string memory _title, string memory _author) public {
        books.push(Book(_title, _author, false));
    }

    function add2(string memory _title, string memory _author) public {
        books.push(Book({title: _title, author: _author, completed: false}));
    }

    function add3(string memory _title, string memory _author) public {
        Book memory book;
        book.title = _title;
        book.author = _author;
        books.push(book);
    }

    function get(uint _index) public view returns (string memory title, string memory author, bool completed) {
        Book storage book = books[_index];
        return (book.title, book.author, book.completed);
    }

    function complete(uint _index) public {
        Book storage book = books[_index];
        book.completed = true;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates read / write / update behavior of structs', async () => {
	const Contract = await ethers.getContractFactory('Structs1');
	let contract = await Contract.deploy();

	await contract.add1('A Tale of Two Cities', 'Charles Dickens');
	await contract.add2('Les Miserables', 'Victor Hugo');
	await contract.add3('The Hobbit', 'J.R.R. Tolkien');

	let result = await contract.get(0);
	expect(result[0]).to.equal('A Tale of Two Cities');
	expect(result[1]).to.equal('Charles Dickens');
	expect(result[2]).to.equal(false);

	// Complete a book
	await contract.complete(0);
	result = await contract.get(0);
	expect(result[2]).to.equal(true);
});
```

---

## **3. Explanation of Struct Operations**

### **Adding Structs**

| Function | Method Used               | Key Takeaways                          |
| -------- | ------------------------- | -------------------------------------- |
| `add1()` | Direct struct creation    | Shortest syntax, inline initialization |
| `add2()` | Named parameters          | More explicit, order-independent       |
| `add3()` | Temporary struct variable | Allows modifying fields before pushing |

### **Retrieving Structs**

-   The `get()` function retrieves a book at a given index.
-   Uses `storage` to access **persistent** values.
-   Returns multiple values (title, author, completed status).

### **Updating Structs**

-   The `complete()` function marks a book as **completed**.
-   Uses `storage` reference to **modify** data **permanently**.

---

## **4. Summary Table**

| Concept             | Key Takeaway                                                      |
| ------------------- | ----------------------------------------------------------------- |
| **Structs**         | Custom data structures to group related variables.                |
| **Adding Data**     | Multiple methods exist (`direct`, `named`, `temporary variable`). |
| **Retrieving Data** | Uses `storage` to return structured information.                  |
| **Updating Data**   | `storage` allows modifying struct fields.                         |

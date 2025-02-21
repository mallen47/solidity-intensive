# Solidity Study Guide: Mappings

## **1. Overview of Mappings**

### **Key Concepts:**

-   A **mapping** is a key-value store in Solidity.
-   Mappings **do not** store keys explicitly—only values for defined keys.
-   Uninitialized mappings return a **default value** (e.g., `0`, `false`, `""`, `address(0)`).
-   Mappings **cannot be iterated over** or retrieved as a list.
-   Mappings can store **nested values**.

---

## **2. Example 1: Basic Mappings**

### **Key Takeaways:**

-   Simple mappings store key-value pairs.
-   Default values exist for non-existent keys.

### **Contract Code:**

```solidity
contract Mappings1 {
    mapping(uint => string) public names;
    mapping(uint => address) public addresses;
    mapping(address => uint) public balances;
    mapping(address => bool) public hasVoted;

    constructor() {
        names[1] = "Adam";
        names[2] = "Ben";
        addresses[1] = 0x3EcEf08D0e2DaD803847E052249bb4F8bFf2D5bB;
        addresses[2] = 0xe5c430b2Dd2150a20f25C7fEde9981f767A48A3c;
        balances[addresses[1]] = 1 ether;
        balances[addresses[2]] = 2 ether;
        hasVoted[addresses[1]] = true;
        hasVoted[addresses[2]] = true;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates basic mappings with default values', async () => {
	const Contract = await ethers.getContractFactory('Mappings1');
	let contract = await Contract.deploy();
	expect(await contract.names(1)).to.equal('Adam');
	expect(await contract.names(2)).to.equal('Ben');
	expect(await contract.names(3)).to.equal(''); // Default value
});
```

---

## **3. Example 2: Mappings with Structs & Nested Mappings**

### **Key Takeaways:**

-   Mappings can store **structs**.
-   **Nested mappings** store mappings within mappings.

### **Contract Code:**

```solidity
contract Mappings2 {
    struct Book {
        string author;
        string title;
    }

    mapping(uint => Book) public books;
    mapping(address => mapping(address => uint)) public balances;

    constructor() {
        books[1] = Book("A Tale of Two Cities", "Charles Dickens");
        books[2] = Book("Les Miserables", "Victor Hugo");
        balances[0x3EcEf08D0e2DaD803847E052249bb4F8bFf2D5bB][0x6B175474E89094C44Da98b954EedeAC495271d0F] = 1 ether;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates mappings with other data types & nested mappings', async () => {
	const Contract = await ethers.getContractFactory('Mappings2');
	let contract = await Contract.deploy();
	let result = await contract.books(1);
	expect(result[0]).to.equal('A Tale of Two Cities');
	expect(result[1]).to.equal('Charles Dickens');
});
```

---

## **4. Example 3: Setting and Removing Values in Mappings**

### **Key Takeaways:**

-   Values in mappings can be **updated** dynamically.
-   `delete` resets the value to its **default state**.

### **Contract Code:**

```solidity
contract Mappings3 {
    mapping(uint => string) public myMapping;

    function get(uint _id) public view returns (string memory) {
        return myMapping[_id];
    }

    function set(uint _id, string memory _value) public {
        myMapping[_id] = _value;
    }

    function remove(uint _id) public {
        delete myMapping[_id];
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates getting and setting values', async () => {
	const Contract = await ethers.getContractFactory('Mappings3');
	let contract = await Contract.deploy();
	await contract.set(1, 'one');
	expect(await contract.get(1)).to.equal('one');
	await contract.remove(1);
	expect(await contract.get(1)).to.equal('');
});
```

---

## **5. Example 4: Nested Mappings - Setting and Removing Values**

### **Key Takeaways:**

-   Mappings can be **nested** for multi-layer storage.
-   `delete` resets nested values.

### **Contract Code:**

```solidity
contract Mappings4 {
    mapping(address => mapping(uint => bool)) public myMapping;

    function get(address _user, uint _id) public view returns (bool) {
        return myMapping[_user][_id];
    }

    function set(address _user, uint _id, bool _value) public {
        myMapping[_user][_id] = _value;
    }

    function remove(address _user, uint _id) public {
        delete myMapping[_user][_id];
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates getting and setting nested values', async () => {
	const Contract = await ethers.getContractFactory('Mappings4');
	let contract = await Contract.deploy();
	let user1 = '0x3EcEf08D0e2DaD803847E052249bb4F8bFf2D5bB';
	await contract.set(user1, 1, true);
	expect(await contract.get(user1, 1)).to.equal(true);
	await contract.remove(user1, 1);
	expect(await contract.get(user1, 1)).to.equal(false);
});
```

---

## **🔹 Summary Table**

| Example | Mapping Type                   | Key Takeaway                                  |
| ------- | ------------------------------ | --------------------------------------------- |
| **1**   | Simple Mapping                 | Stores key-value pairs, default values exist. |
| **2**   | Struct & Nested Mapping        | Stores complex data structures.               |
| **3**   | Dynamic Mappings               | Values can be updated and removed.            |
| **4**   | Nested Mapping (Multiple Keys) | Enables layered access control.               |

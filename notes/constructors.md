# Solidity Study Guide: Constructors

## **1. Constructors Overview**

### **Key Concepts:**

-   A **constructor** is a special function that **executes once** when the contract is deployed.
-   Used to **initialize** contract state variables.
-   Can **accept arguments**.
-   Can be **payable**, allowing it to receive Ether at deployment.
-   Can be **inherited** from parent contracts.

---

## **2. Example 1: No Constructor**

### **Key Takeaways:**

-   If a contract **does not define a constructor**, Solidity provides a default one.
-   State variables are initialized with default values.

### **Contract Code:**

```solidity
contract Constructors1 {
    string public name = "Example 1";
}
```

### **Test Explanation:**

```javascript
it('it does not have a constructor', async () => {
	const Contract = await ethers.getContractFactory('Constructors1');
	let contract = await Contract.deploy();
	expect(await contract.name()).to.equal('Example 1');
});
```

-   Since there is **no constructor**, `name` is initialized to "Example 1" by default.

---

## **3. Example 2: Constructor Without Arguments**

### **Key Takeaways:**

-   The constructor runs once at deployment.
-   No parameters are passed.

### **Contract Code:**

```solidity
contract Constructors2 {
    string public name;
    constructor() {
        name = "Example 2";
    }
}
```

### **Test Explanation:**

```javascript
it('has a constructor with no arguments', async () => {
	const Contract = await ethers.getContractFactory('Constructors2');
	let contract = await Contract.deploy();
	expect(await contract.name()).to.equal('Example 2');
});
```

-   The contract is initialized with `name = "Example 2"` inside the constructor.
-   The test checks that `name` is correctly set.

---

## **4. Example 3: Constructor With Arguments**

### **Key Takeaways:**

-   Constructors can accept **arguments** to initialize state variables dynamically.

### **Contract Code:**

```solidity
contract Constructors3 {
    string public name;
    constructor(string memory _name) {
        name = _name;
    }
}
```

### **Test Explanation:**

```javascript
it('has a constructor with arguments', async () => {
	const Contract = await ethers.getContractFactory('Constructors3');
	let contract = await Contract.deploy('Example 3');
	expect(await contract.name()).to.equal('Example 3');
});
```

-   Deploys `Constructors3` with "Example 3" as an argument.
-   The test ensures that `name` was set correctly.

---

## **5. Example 4: Payable Constructor**

### **Key Takeaways:**

-   Constructors can be `payable`, allowing the contract to receive Ether upon deployment.
-   The contract balance should reflect the received Ether.

### **Contract Code:**

```solidity
contract Constructors4 {
    string public name;
    constructor() payable {
        name = "Example 4";
    }
}
```

### **Test Explanation:**

```javascript
it('has a payable constructor', async () => {
	const Contract = await ethers.getContractFactory('Constructors4');
	let contract = await Contract.deploy({ value: ether(1) });
	let balance = await ethers.provider.getBalance(contract.address);
	expect(ethers.utils.formatEther(balance)).to.equal('1.0');
});
```

-   Deploys `Constructors4` while sending `1 Ether`.
-   Verifies the contract's balance to ensure it received the Ether.

---

## **6. Example 5: Inheriting a Constructor**

### **Key Takeaways:**

-   Contracts **can inherit constructors** from parent contracts.
-   The parent constructor initializes inherited state variables.

### **Contract Code:**

```solidity
contract Parent1 {
    string public name;
    constructor() {
        name = "Example 5";
    }
}
contract Constructors5 is Parent1 {
    string public description = "This contract inherits from Parent 1";
}
```

### **Test Explanation:**

```javascript
it('inherits the constructor', async () => {
	const Contract = await ethers.getContractFactory('Constructors5');
	let contract = await Contract.deploy();
	expect(await contract.name()).to.equal('Example 5');
});
```

-   `Constructors5` inherits from `Parent1`, which initializes `name = "Example 5"`.
-   The test ensures inheritance works as expected.

---

## **7. Example 6: Extending a Parent Constructor**

### **Key Takeaways:**

-   A child contract can **extend** a parent contract’s constructor by passing additional arguments.

### **Contract Code:**

```solidity
contract Parent2 {
    string public name;
    constructor(string memory _name) {
        name = _name;
    }
}
contract Constructors6 is Parent2 {
    string public description;
    constructor(string memory _name, string memory _description) Parent2(_name) {
        description = _description;
    }
}
```

### **Test Explanation:**

```javascript
it('extends the parent constructor', async () => {
	const Contract = await ethers.getContractFactory('Constructors6');
	let contract = await Contract.deploy(
		'Example 6',
		'This contract inherits from Parent 2'
	);
	expect(await contract.name()).to.equal('Example 6');
});
```

-   Deploys `Constructors6` with two arguments.
-   Ensures that **both the parent and child constructors** initialize state correctly.

---

## **🔹 Summary Table**

| Example | Constructor Type             | Key Takeaway                                                     |
| ------- | ---------------------------- | ---------------------------------------------------------------- |
| **1**   | No Constructor               | Solidity provides a default constructor.                         |
| **2**   | Constructor (No Arguments)   | Initializes state without external input.                        |
| **3**   | Constructor (With Arguments) | Allows dynamic initialization at deployment.                     |
| **4**   | Payable Constructor          | Enables the contract to receive Ether at deployment.             |
| **5**   | Inherited Constructor        | Child contract inherits parent constructor.                      |
| **6**   | Extended Constructor         | Child contract extends parent constructor with extra parameters. |

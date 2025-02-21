# Solidity Study Guide: Inheritance

## **1. Overview of Inheritance**

### **Key Concepts:**

-   **Inheritance** allows a contract to derive properties and functions from a parent contract.
-   **Modifiers and constructors** can be inherited from parent contracts.
-   Solidity supports **multiple inheritance**, but function overrides must be handled explicitly.
-   **`super` keyword** allows calling parent contract methods in child contracts.

---

## **2. Example 1: Simple Inheritance**

### **Key Takeaways:**

-   `Inheritance1` **inherits** from `Ownable`.
-   The `onlyOwner` modifier is inherited and applied to `setName()`.
-   The constructor from `Ownable` initializes the `owner`.

### **Contract Code:**

```solidity
contract Ownable {
    address owner;
    constructor() {
        owner = msg.sender;
    }
    modifier onlyOwner {
        require(msg.sender == owner, 'caller must be owner');
        _;
    }
}

contract Inheritance1 is Ownable {
    string public name = "Example 1";
    function setName(string memory _name) public onlyOwner {
        name = _name;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates simple inheritance from 1 contract', async () => {
	const Contract = await ethers.getContractFactory('Inheritance1');
	let contract = await Contract.deploy();
	await contract.setName('New name');
	expect(await contract.name()).to.equal('New name');
});
```

---

## **3. Example 2: Multiple Inheritance**

### **Key Takeaways:**

-   `Inheritance2` inherits from both `Ownable` (ownership) and `Payable` (Ether receipt capability).
-   The contract can **store and withdraw** Ether.

### **Contract Code:**

```solidity
contract Payable {
    receive() external payable {}
}

contract Inheritance2 is Ownable, Payable {
    function withdraw() public onlyOwner {
        uint256 value = address(this).balance;
        (bool sent, ) = owner.call{value: value}("");
        require(sent);
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates inheritance from multiple contracts', async () => {
	const Contract = await ethers.getContractFactory('Inheritance2');
	let contract = await Contract.deploy();

	accounts = await ethers.getSigners();
	owner = accounts[0];

	await owner.sendTransaction({ to: contract.address, value: ether(1) });
	expect(await ethers.provider.getBalance(contract.address)).to.equal(
		ether(1)
	);
	await contract.withdraw();
	expect(await ethers.provider.getBalance(contract.address)).to.equal(0);
});
```

---

## **4. Example 3: Constructor Inheritance**

### **Key Takeaways:**

-   `Inheritance3` inherits from `Token1` and passes an argument to its constructor.
-   The child contract initializes its own additional variables.

### **Contract Code:**

```solidity
contract Token1 {
    uint public totalSupply;
    constructor(uint _totalSupply) {
        totalSupply = _totalSupply;
    }
}

contract Inheritance3 is Token1 {
    string public name;
    string public symbol;
    uint public decimals;
    constructor() Token1(1000000 * (10 ** 18)) {
        name = "My Token";
        symbol = "MTK";
        decimals = 18;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates simple constructor inheritance', async () => {
	const Contract = await ethers.getContractFactory('Inheritance3');
	let contract = await Contract.deploy();

	expect(await contract.name()).to.equal('My Token');
	expect(await contract.symbol()).to.equal('MTK');
	expect(await contract.decimals()).to.equal(18);
	expect(await contract.totalSupply()).to.equal(tokens(1000000)); // 1 million
});
```

---

## **5. Example 4: Function Override with `super` and Constructor**

### **Key Takeaways:**

-   `Inheritance4` overrides `balanceOf()` from `Token2`.
-   Uses `super.balanceOf(_account)` to **inherit and modify behavior**.

### **Contract Code:**

```solidity
contract Token2 {
    uint public totalSupply;
    string public name = "My Token";
    string public symbol = "MTK";
    uint public decimals = 18;

    mapping (address => uint) balances;

    constructor(uint _totalSupply) {
        totalSupply = _totalSupply * (10 ** 18);
        balances[msg.sender] = totalSupply;
    }

    function balanceOf(address _account) virtual public view returns(uint) {
        return balances[_account];
    }
}

contract Inheritance4 is Token2 {
    constructor(uint _totalSupply) Token2(_totalSupply) {}

    function balanceOf(address _account) virtual override public view returns(uint) {
        uint balance = super.balanceOf(_account);
        return balance * 10;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates function override with super & constructor', async () => {
	const Contract = await ethers.getContractFactory('Inheritance4');
	let contract = await Contract.deploy(1000000);

	accounts = await ethers.getSigners();
	owner = accounts[0];

	expect(await contract.name()).to.equal('My Token');
	expect(await contract.symbol()).to.equal('MTK');
	expect(await contract.decimals()).to.equal(18);
	expect(await contract.totalSupply()).to.equal(tokens(1000000)); // 1 million
	expect(await contract.balanceOf(owner.address)).to.equal(tokens(10000000)); // 10 million
});
```

---

## **6. Summary Table**

| Concept                     | Key Takeaway                                                                           |
| --------------------------- | -------------------------------------------------------------------------------------- |
| **Single Inheritance**      | Contracts can inherit variables and functions from a single parent.                    |
| **Multiple Inheritance**    | A contract can inherit from multiple parent contracts.                                 |
| **Constructor Inheritance** | Constructors from parent contracts can be inherited and initialized.                   |
| **Function Overriding**     | Child contracts can override parent contract functions using `virtual` and `override`. |
| **`super` Keyword**         | Calls the parent contract’s overridden function.                                       |

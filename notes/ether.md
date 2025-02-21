# Solidity Study Guide: Ether Transactions & Units

## **1. Overview of Ether in Solidity**

### **Key Concepts:**

-   **Ether Units:** `wei`, `gwei`, and `ether` are different denominations of Ether.
-   **Receiving Ether:** Contracts must implement `receive()` or `fallback()` to accept Ether.
-   **Transferring Ether:** Ether is sent using `.transfer()`, `.call()`, or `.send()` (though `.call()` is recommended).
-   **Payable Functions:** Functions that handle Ether must be marked `payable`.

---

## **2. Example 1: Ether Units**

### **Key Takeaways:**

-   Ether has different units: `wei`, `gwei`, and `ether`.
-   1 `ether` = 1,000,000,000 `gwei` = 1,000,000,000,000,000,000 `wei`.

### **Contract Code:**

```solidity
contract Ether1 {
    uint public value1 = 1 wei;
    uint public value2 = 1;
    uint public value3 = 1 gwei;
    uint public value4 = 1000000000;
    uint public value5 = 1 ether;
    uint public value6 = 1000000000000000000;
}
```

### **Test Explanation:**

```javascript
it('demonstrates Ether units', async () => {
	const Contract = await ethers.getContractFactory('Ether1');
	contract = await Contract.deploy();

	expect(await contract.value1()).to.equal(await contract.value2());
	expect(await contract.value3()).to.equal(await contract.value4());
	expect(await contract.value5()).to.equal(await contract.value6());
});
```

---

## **3. Example 2: Receiving Ether with `receive()`**

### **Key Takeaways:**

-   The `receive()` function allows a contract to receive Ether.
-   `msg.data` **must be empty** for `receive()` to be triggered.

### **Contract Code:**

```solidity
contract Ether2 {
    receive() external payable {}
}
```

### **Test Explanation:**

```javascript
it('demonstrates "receive()" function', async () => {
	const Contract = await ethers.getContractFactory('Ether2');
	contract = await Contract.deploy();

	accounts = await ethers.getSigners();
	owner = accounts[0];

	await owner.sendTransaction({ to: contract.address, value: ether(1) });
	expect(await ethers.provider.getBalance(contract.address)).to.equal(
		ether(1)
	);
});
```

---

## **4. Example 3: Handling Transactions with `fallback()`**

### **Key Takeaways:**

-   `fallback()` is triggered when `msg.data` is **not empty** or when `receive()` is not implemented.
-   Allows contract logic execution on Ether receipt.

### **Contract Code:**

```solidity
contract Ether3 {
    uint public count = 0;
    fallback() external payable {
        count++;
    }
    function checkBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates "fallback()" function', async () => {
	const Contract = await ethers.getContractFactory('Ether3');
	contract = await Contract.deploy();

	accounts = await ethers.getSigners();
	owner = accounts[0];

	await owner.sendTransaction({ to: contract.address, value: ether(1) });
	expect(await contract.checkBalance()).to.equal(ether(1));
	expect(await contract.count()).to.equal(1);
});
```

---

## **5. Example 4: Transferring Ether**

### **Key Takeaways:**

-   **`.transfer()`** is **not recommended** due to gas limitations.
-   **`.call{value: amount}()`** is **preferred** for sending Ether.

### **Contract Code:**

```solidity
contract Ether4 {
    function transfer1(address payable _to) public payable {
        _to.transfer(msg.value);
    }

    function transfer2(address payable _to) public payable {
        (bool sent, ) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates transferring Ether', async () => {
	const Contract = await ethers.getContractFactory('Ether4');
	contract = await Contract.deploy();

	let receiver = '0x3EcEf08D0e2DaD803847E052249bb4F8bFf2D5bB';
	await contract.transfer1(receiver, { value: ether(1) });
	expect(await ethers.provider.getBalance(receiver)).to.equal(ether(1));

	await contract.transfer2(receiver, { value: ether(1) });
	expect(await ethers.provider.getBalance(receiver)).to.equal(ether(2));
});
```

---

## **6. Example 5: Payable Functions**

### **Key Takeaways:**

-   Functions handling Ether must be marked `payable`.
-   A non-payable function will **revert** if Ether is sent.

### **Contract Code:**

```solidity
contract Ether5 {
    uint public balance;

    function deposit2() public payable {
        balance += msg.value;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates payable functions', async () => {
	const Contract = await ethers.getContractFactory('Ether5');
	contract = await Contract.deploy();

	await contract.deposit2({ value: ether(1) });
	expect(await ethers.provider.getBalance(contract.address)).to.equal(
		ether(1)
	);
});
```

---

## **7. Summary Table**

| Concept                | Key Takeaway                                                    |
| ---------------------- | --------------------------------------------------------------- |
| **Ether Units**        | `wei`, `gwei`, `ether` are different denominations.             |
| **Receiving Ether**    | Implement `receive()` to accept plain Ether transfers.          |
| **Fallback Function**  | Handles unexpected transactions or data-bearing calls.          |
| **Transferring Ether** | `.call{value: amount}()` is **recommended** over `.transfer()`. |
| **Payable Functions**  | Must be explicitly marked `payable` to handle Ether.            |

# Solidity Study Guide: Time-Based Restrictions

## **1. Overview of Time-Based Restrictions**

### **Key Concepts:**

-   **`block.timestamp`** provides the current Unix timestamp of the blockchain.
-   **Time-based conditions** ensure actions occur only after a specific period.
-   **Modifiers** are used to enforce time-based access control.

---

## **2. Example 1: Time-Based Deposits & Withdrawals**

### **Key Takeaways:**

-   The contract restricts deposits and withdrawals based on **timestamps**.
-   The **`depositStartTime`** and **`withdrawStartTime`** are set in the constructor.
-   Functions require **time conditions** before execution.

### **Contract Code:**

```solidity
contract Time1 {
    address public owner;
    uint public depositStartTime;
    uint public withdrawStartTime;

    modifier onlyOwner {
        require(msg.sender == owner, 'caller must be owner');
        _;
    }

    constructor(uint _depositStartTime, uint _withdrawStartTime) {
        owner = msg.sender;
        depositStartTime = _depositStartTime;
        withdrawStartTime = _withdrawStartTime;
    }

    function deposit() public payable onlyOwner {
        require(block.timestamp >= depositStartTime, 'cannot deposit yet');
    }

    modifier afterWithdrawEnabled {
        require(
            block.timestamp >= withdrawStartTime,
            'cannot withdraw yet'
        );
        _;
    }

    function withdraw() public onlyOwner afterWithdrawEnabled {
        uint256 value = address(this).balance;
        (bool sent, ) = owner.call{value: value}("");
        require(sent);
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates time restriction with block.timestamp', async () => {
	let now = await time.latest();

	let depositStartTime = now + 1000; // add 1,000 seconds
	let withdrawStartTime = now + 2000; // add 2,000 seconds

	const Contract = await ethers.getContractFactory('Time1');
	contract = await Contract.deploy(depositStartTime, withdrawStartTime);

	// Try to deposit before allowed time
	await expect(contract.deposit({ value: ether(1) })).to.be.reverted;

	// Advance time past deposit start time
	await time.increaseTo(depositStartTime + 1);

	// Deposit successfully
	await contract.deposit({ value: ether(1) });
	expect(await ethers.provider.getBalance(contract.address)).to.equal(
		ether(1)
	);

	// Try to withdraw before allowed time
	await expect(contract.withdraw()).to.be.reverted;

	// Advance time past withdraw start time
	await time.increaseTo(withdrawStartTime + 1);

	// Withdraw successfully
	await contract.withdraw();
	expect(await ethers.provider.getBalance(contract.address)).to.equal(
		ether(0)
	);
});
```

---

## **3. Explanation of Time-Based Functions**

### **Using `block.timestamp` for Time Checks**

-   The contract uses `block.timestamp` to enforce time constraints.
-   Example:
    ```solidity
    require(block.timestamp >= depositStartTime, 'cannot deposit yet');
    ```

### **Using Modifiers for Time Constraints**

-   **Restrict deposits:** `onlyOwner` ensures only the contract owner can deposit.
-   **Restrict withdrawals:** `afterWithdrawEnabled` prevents premature withdrawals.

### **Testing Time-Based Functions**

-   **Simulating time changes:**
    ```javascript
    await time.increaseTo(depositStartTime + 1);
    ```
-   **Ensuring deposits fail before allowed time:**
    ```javascript
    await expect(contract.deposit({ value: ether(1) })).to.be.reverted;
    ```

---

## **4. Summary Table**

| Concept                        | Key Takeaway                                                 |
| ------------------------------ | ------------------------------------------------------------ |
| **`block.timestamp`**          | Retrieves the current Unix time.                             |
| **Time-Based Modifiers**       | Restrict function execution until a specific time.           |
| **Testing Time-Based Actions** | Hardhat's `time.increaseTo()` simulates time changes.        |
| **Reverting Transactions**     | `require(block.timestamp >= time)` prevents early execution. |

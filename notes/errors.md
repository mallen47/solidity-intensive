# Solidity Study Guide: Error Handling

## **1. Overview of Error Handling in Solidity**

### **Key Concepts:**

-   Solidity provides multiple ways to handle errors: `require()`, `revert()`, `assert()`, and custom errors.
-   `require()` is used for **input validation** and **access control**.
-   `revert()` is used for **conditional failure handling** inside an `if` statement.
-   `assert()` is used for **internal logic validation** and checks invariants.
-   Custom errors provide **gas-efficient** ways to define error messages.

---

## **2. Example 1: Basic Error Handling**

### **Key Takeaways:**

-   `example1()` uses `require()` to check a condition and **reverts with a message** if it fails.
-   `example2()` uses an `if` statement and `revert()`.
-   `example3()` uses `assert()` to verify an internal condition.
-   `example4()` demonstrates a **custom error**.

### **Contract Code:**

```solidity
contract Errors1 {
    event Log(string message);

    function example1(uint _value) public {
        require(_value > 10, "must be greater than 10");
        emit Log("success");
    }

    function example2(uint _value) public {
        if (_value <= 10) {
            revert("must be greater than 10");
        }
        emit Log("success");
    }

    function example3(uint _value) public {
        assert(_value == 10);
        emit Log("success");
    }

    error InvalidValue(uint value);

    function example4(uint _value) public {
        if (_value <= 10) {
            revert InvalidValue({value: _value});
        }
        emit Log("success");
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates error handling', async () => {
	const Contract = await ethers.getContractFactory('Errors1');
	contract = await Contract.deploy();

	await expect(contract.example1(5)).to.be.reverted;
	await expect(contract.example1(20)).to.be.fulfilled;

	await expect(contract.example2(5)).to.be.reverted;
	await expect(contract.example2(20)).to.be.fulfilled;

	await expect(contract.example3(5)).to.be.reverted;
	await expect(contract.example3(10)).to.be.fulfilled;

	await expect(contract.example4(5)).to.be.reverted;
	await expect(contract.example4(20)).to.be.fulfilled;
});
```

---

## **3. Explanation of Solidity Error Handling Methods**

### **1️⃣ `require(condition, message)`**

-   Used for **input validation**.
-   Reverts if the condition is `false`.
-   **Common use cases:**
    -   Checking function arguments.
    -   Ensuring only authorized users can call a function.
-   **Example:**
    ```solidity
    require(_value > 10, "must be greater than 10");
    ```

### **2️⃣ `revert(message)`**

-   Used **inside an `if` statement** to trigger a failure.
-   Useful when conditions are **more complex than `require()` can handle**.
-   **Example:**
    ```solidity
    if (_value <= 10) {
        revert("must be greater than 10");
    }
    ```

### **3️⃣ `assert(condition)`**

-   Used for **internal state validation**.
-   Typically checks for conditions that **should never fail** (e.g., mathematical invariants).
-   Consumes **all remaining gas** if it fails.
-   **Example:**
    ```solidity
    assert(_value == 10);
    ```

### **4️⃣ Custom Errors (`error ErrorName(parameters)`)**

-   More **gas-efficient** than `require()` or `revert()`.
-   Can include **detailed failure information**.
-   **Example:**
    ```solidity
    error InvalidValue(uint value);
    revert InvalidValue({value: _value});
    ```

---

## **4. Summary Table**

| Error Handling Method | Use Case                                      | Gas Cost                          |
| --------------------- | --------------------------------------------- | --------------------------------- |
| **require()**         | Input validation & access control             | Low                               |
| **revert()**          | Complex conditions inside `if` statements     | Medium                            |
| **assert()**          | Internal logic verification                   | High (all remaining gas consumed) |
| **Custom Errors**     | More detailed, gas-efficient failure handling | Low                               |

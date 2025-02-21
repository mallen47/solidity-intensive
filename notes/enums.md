# Solidity Study Guide: Enums

## **1. Overview of Enums**

### **Key Concepts:**

-   **Enums** (Enumerations) are user-defined types that represent a fixed set of named values.
-   They improve **code readability** and **prevent invalid states**.
-   The **default value** of an enum is the **first listed value** (e.g., `Todo` → `0`).
-   Enums can be **retrieved, modified, and reset**.

---

## **2. Example 1: Basic Enum Usage**

### **Key Takeaways:**

-   The `Status` enum has three possible values: `Todo`, `InProgress`, `Done`.
-   The default status is `Todo` (`0`).
-   Status can be **manually set, updated, or reset**.

### **Contract Code:**

```solidity
contract Enums1 {
    enum Status {
        Todo,
        InProgress,
        Done
    }

    Status public status;

    function get() public view returns (Status) {
        return status;
    }

    function set(Status _status) public {
        status = _status;
    }

    function complete() public {
        status = Status.Done;
    }

    function reset() public {
        delete status;
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates read / write / update behavior of enums', async () => {
	const Contract = await ethers.getContractFactory('Enums1');
	let contract = await Contract.deploy();

	// Defaults to 0 (Todo)
	expect(await contract.get()).to.equal(0);

	// Set to "In Progress" (1)
	await contract.set(1);
	expect(await contract.get()).to.equal(1);

	// Mark as Done (2)
	await contract.complete();
	expect(await contract.get()).to.equal(2);

	// Reset to default (0, Todo)
	await contract.reset();
	expect(await contract.get()).to.equal(0);
});
```

---

## **3. Explanation of Enum Operations**

### **Default Behavior**

-   The **default enum value** is the **first item listed**.
-   For `Status`, the default is `Todo` (`0`).

### **Updating Enums**

-   `set(Status _status)`: Sets status to any value from the enum.
-   `complete()`: Sets status to `Done` (`2`).
-   `reset()`: Resets status to its default (`Todo` or `0`).

### **Mapping of Enum Values**

| Enum Name    | Index Value |
| ------------ | ----------- |
| `Todo`       | `0`         |
| `InProgress` | `1`         |
| `Done`       | `2`         |

---

## **4. Summary Table**

| Concept            | Key Takeaway                                                     |
| ------------------ | ---------------------------------------------------------------- |
| **Enums**          | Used for **fixed sets of values** to improve readability.        |
| **Default Value**  | Always the **first item** in the enum list (e.g., `0`).          |
| **Updating Enums** | Use functions like `set()`, `complete()`, `reset()`.             |
| **Testing**        | Ensure default, manual setting, and reset behaviors are correct. |

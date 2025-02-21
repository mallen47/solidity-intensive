# Solidity Study Guide: Functions

## **1. Functions1: Basic Read & Write Functions**

### **Key Concepts:**

-   **Read functions (`view`) are free** (do not modify the blockchain state).
-   **Write functions cost gas** (modify state variables).
-   **Functions can have arguments or no arguments.**

### **Code Breakdown:**

```solidity
contract Functions1 {
    string name = "Example 1";

    function setName(string memory _name) public {
        name = _name;
    }

    function getName() public view returns(string memory) {
        return name;
    }

    function resetName() public {
        name = "Example 1";
    }
}
```

-   `setName(string memory _name)`: Updates the `name` variable.
-   `getName() view`: Reads and returns the `name` variable.
-   `resetName()`: Resets `name` to "Example 1".

---

## **2. Functions2: Calling Functions Within a Contract**

### **Key Concepts:**

-   Functions can call **other functions** within the contract.
-   Only `public` functions are accessible externally.

### **Code Breakdown:**

```solidity
contract Functions2 {
    uint public count;

    function increment() public {
        count = add(count, 1);
    }

    function add(uint a, uint b) internal pure returns(uint) {
        return a + b;
    }
}
```

-   `increment()`: Calls `add()` to increase `count`.
-   `add(uint a, uint b) internal pure`: Helper function to perform addition, but **cannot be called externally**.

---

## **3. Functions3: Defining Functions Outside Contracts**

### **Key Concepts:**

-   Functions **can exist outside contracts**.
-   External functions can be **called inside contracts**.

### **Code Breakdown:**

```solidity
function addNumbers(uint a, uint b) pure returns(uint) {
    return a + b;
}

contract Functions3 {
    uint public count;

    function increment() public {
        count = addNumbers(count, 1);
    }
}
```

-   `addNumbers(uint a, uint b)`: A function **defined outside the contract**.
-   `increment()`: Calls `addNumbers()` to increase `count`.

---

## **4. Functions4: Visibility Modifiers**

### **Key Concepts:**

| Visibility | Meaning                                                         |
| ---------- | --------------------------------------------------------------- |
| `public`   | Visible **internally & externally**                             |
| `private`  | Only accessible **inside the contract**                         |
| `external` | Only callable **from outside the contract** (via `this.func()`) |
| `internal` | Accessible **within the contract and derived contracts**        |

### **Code Breakdown:**

```solidity
contract Functions4 {
    uint public count;

    function increment1() public {
        count = count + 1;
    }

    function increment2() public {
        increment1();
    }

    function increment3() private {
        count = count + 1;
    }

    function increment4() public {
        increment3();
    }

    function increment5() external {
        count = count + 1;
    }

    function increment6() internal {
        count = count + 1;
    }

    function increment7() public {
        increment6();
    }
}
```

-   `increment3() private`: **Can be called internally** but **not externally**.
-   `increment5() external`: **Cannot be called from within the contract**.
-   `increment6() internal`: **Can be called within the contract or derived contracts**.

---

## **5. Functions5: Modifiers (`view`, `pure`, `payable`)**

### **Key Concepts:**

-   **`view`**: Reads state but does not modify it.
-   **`pure`**: Does not read or modify state.
-   **`payable`**: Allows the function to receive Ether.

### **Code Breakdown:**

```solidity
contract Functions5 {
    string public name = "Example 5";
    uint public balance;

    function getName() public view returns(string memory) {
        return name;
    }

    function add(uint a, uint b) public pure returns(uint) {
        return a + b;
    }

    function pay() public payable {
        balance = msg.value;
    }
}
```

-   `getName() view`: **Reads state but does not modify it**.
-   `add(uint a, uint b) pure`: **Does not access state variables**.
-   `pay() payable`: **Allows sending ETH to the contract**.

---

## **6. Functions6: Custom Modifiers**

### **Key Concepts:**

-   Custom modifiers **enforce conditions before function execution**.
-   **Example:** `onlyOwner` restricts access to the contract owner.

### **Code Breakdown:**

```solidity
contract Functions6 {
    address private owner;
    string public name = "";
    bool private nameSet = false;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner {
        require(msg.sender == owner, 'caller must be owner');
        _;
    }

    modifier onlyOnce {
        require(nameSet == false, 'can only set name once');
        _;
    }

    function setName1(string memory _name) onlyOwner public {
        name = _name;
    }

    function setName2(string memory _name) onlyOwner onlyOnce public {
        name = _name;
        nameSet = true;
    }
}
```

-   `onlyOwner`: Restricts function execution **to the contract owner**.
-   `onlyOnce`: Ensures `setName2()` can **only be executed once**.

---

## **7. Functions7: Return Values & Events**

### **Key Concepts:**

-   Functions can return **multiple values**.
-   **Event logs** store transaction history (used for off-chain interactions).

### **Code Breakdown:**

```solidity
contract Functions7 {
    string name = "Example 7";

    event NameChanged(string name);

    function getName1() public view returns(string memory) {
        return name;
    }

    function getName6() public view returns(string memory name1, string memory name2) {
        return(name, "New name");
    }

    function setName1() public returns(string memory) {
        name = "New name";
        emit NameChanged(name);
        return name;
    }
}
```

-   `getName6()`: **Returns multiple values** (`name` and `"New name"`).
-   `emit NameChanged(name)`: **Logs an event** when the name is changed.

---

## **🔹 Summary Table**

| Feature        | Key Takeaways                               |
| -------------- | ------------------------------------------- |
| Function Types | `view`, `pure`, `payable`                   |
| Visibility     | `public`, `private`, `external`, `internal` |
| Modifiers      | Custom conditions (`onlyOwner`, `onlyOnce`) |
| Events         | Used for **off-chain tracking**             |
| Return Values  | **Single or multiple values**               |

# Solidity Study Guide: Contract Interactions

## **1. Overview of Contract Interactions**

### **Key Concepts:**

-   Contracts can **interact** with other contracts by calling their functions.
-   Contract **addresses** are passed into constructors for interaction.
-   **Interfaces** allow interaction with external contracts without needing full implementation.
-   **ERC-20 token interactions** require `transferFrom()` for token deposits.

---

## **2. Example 1: Basic Contract Interaction**

### **Key Takeaways:**

-   `SecretVault` stores a private string and exposes functions to read/write it.
-   `Interactions1` interacts with `SecretVault` to modify the secret.

### **Contract Code:**

```solidity
contract SecretVault {
    string private secret;

    constructor(string memory _secret) {
        secret = _secret;
    }

    function setSecret(string memory _secret) external {
        secret = _secret;
    }

    function getSecret() external view returns(string memory) {
        return secret;
    }
}

contract Interactions1 {
    SecretVault public secretVault;

    constructor(SecretVault _secretVault) {
        secretVault = _secretVault;
    }

    function setSecret(string memory _secret) public {
        secretVault.setSecret(_secret);
    }

    function getSecret() public view returns(string memory) {
        return secretVault.getSecret();
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates simple contract interaction', async () => {
	const SecretVault = await ethers.getContractFactory('SecretVault');
	let secretVault = await SecretVault.deploy('Secret');

	const Contract = await ethers.getContractFactory('Interactions1');
	let contract = await Contract.deploy(secretVault.address);

	expect(await contract.getSecret()).to.equal('Secret');
	await contract.setSecret('New Secret');
	expect(await contract.getSecret()).to.equal('New Secret');
});
```

---

## **3. Example 2: ERC-20 Token Interaction**

### **Key Takeaways:**

-   **Interfaces** define functions from external contracts.
-   `Interactions2` interacts with an **ERC-20 token contract** using `transferFrom()`.
-   The contract deposits tokens from the sender to itself.

### **Contract Code:**

```solidity
import "./Token.sol";

interface IERC20 {
    function transferFrom(
        address _from,
        address _to,
        uint256 _value
    )
        external
        returns (bool success);
}

contract Interactions2 {
    function deposit(address _tokenAddress, uint _amount) public {
        IERC20(_tokenAddress).transferFrom(msg.sender, address(this), _amount);
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates ERC-20 token contract interaction', async () => {
	const Token = await ethers.getContractFactory('Token');
	let token = await Token.deploy('My Token', 'MTK', tokens(1000000));

	const Contract = await ethers.getContractFactory('Interactions2');
	let contract = await Contract.deploy();

	accounts = await ethers.getSigners();
	owner = accounts[0];

	// Approve tokens
	await token.approve(contract.address, tokens(1000000));
	// Deposit
	await contract.deposit(token.address, tokens(1000000));

	expect(await token.balanceOf(contract.address)).to.equal(tokens(1000000));
});
```

---

## **4. Explanation of Contract Interaction Techniques**

### **Passing Contract Addresses**

-   `Interactions1` is initialized with an instance of `SecretVault`.
-   This allows one contract to **directly call another**.

### **Using Interfaces for External Calls**

-   `IERC20` is an **interface** that defines `transferFrom()`.
-   Allows `Interactions2` to **interact with any ERC-20 token contract**.

### **ERC-20 Token Interaction**

-   `transferFrom(msg.sender, address(this), _amount)` moves tokens **from sender to contract**.
-   The sender **must first approve** the contract to transfer tokens.

---

## **5. Summary Table**

| Concept                        | Key Takeaway                                                                              |
| ------------------------------ | ----------------------------------------------------------------------------------------- |
| **Contract Interaction**       | Contracts can interact by calling functions from deployed contracts.                      |
| **Passing Contract Addresses** | Enables direct function calls from one contract to another.                               |
| **Using Interfaces**           | Interfaces allow interaction with external contracts without needing full implementation. |
| **ERC-20 Token Deposits**      | Requires `approve()` and `transferFrom()` for token transfers.                            |

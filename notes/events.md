# Solidity Study Guide: Events

## **1. Overview of Events**

### **Key Concepts:**

-   **Events** in Solidity allow contracts to communicate with off-chain applications.
-   They are stored in transaction logs and provide historical data.
-   Events can have **up to 17 arguments** (including up to **3 indexed arguments** for filtering).
-   Events **do not consume much gas** as they are not stored in contract storage.

---

## **2. Example 1: Basic Event Usage**

### **Key Takeaways:**

-   The `MessageUpdated` event logs messages and the address of the sender.
-   The `updateMessage()` function updates `message` and emits the event.

### **Contract Code:**

```solidity
contract Events1 {
    string public message = "Hello World";

    event MessageUpdated(
        address indexed _user,
        string _message
    );

    function updateMessage(string memory _message) public {
        message = _message;
        emit MessageUpdated(msg.sender, _message);
    }
}
```

### **Test Explanation:**

```javascript
it('demonstrates basic event subscription and history', async () => {
	const Contract = await ethers.getContractFactory('Events1');
	let contract = await Contract.deploy();

	// Call updateMessage and check real-time event log
	let transaction = await contract.updateMessage('Hey!');
	await transaction.wait();
	await expect(transaction)
		.to.emit(contract, 'MessageUpdated')
		.withArgs(user1.address, 'Hey!');

	// Call updateMessage multiple times to generate event history
	await contract.updateMessage('Ho!');
	await contract.updateMessage('Ha!');

	// Retrieve all past events
	let eventStream = await contract.queryFilter('MessageUpdated');
	expect(eventStream.length).to.equal(3);

	// Validate first event data
	let firstEvent = eventStream[0];
	expect(firstEvent.args[1]).to.equal('Hey!');

	// Trigger event from another user (user2)
	transaction = await contract.connect(user2).updateMessage('Huh!');
	await transaction.wait();

	// Filter events for user2 only
	let user2Filter = contract.filters.MessageUpdated(user2.address, null);
	eventStream = await contract.queryFilter(user2Filter);
	expect(eventStream.length).to.equal(1);

	// Validate user2's event
	firstEvent = eventStream[0];
	expect(firstEvent.args[1]).to.equal('Huh!');
});
```

---

## **3. Explanation of Event Operations**

### **Emitting an Event**

-   The `emit` keyword triggers an event.
-   Example:
    ```solidity
    emit MessageUpdated(msg.sender, _message);
    ```
-   This logs:
    -   The `msg.sender` (who updated the message)
    -   The new message value

### **Filtering Events**

-   **Indexed parameters** (`indexed _user`) enable efficient filtering.
-   Example of filtering events by user address:
    ```javascript
    let user2Filter = contract.filters.MessageUpdated(user2.address, null);
    let eventStream = await contract.queryFilter(user2Filter);
    ```

### **Retrieving Event History**

-   Querying all past `MessageUpdated` events:
    ```javascript
    let eventStream = await contract.queryFilter('MessageUpdated');
    ```

---

## **4. Summary Table**

| Concept                      | Key Takeaway                                                     |
| ---------------------------- | ---------------------------------------------------------------- |
| **Events**                   | Allow logging important contract interactions.                   |
| **Indexed Arguments**        | Enable efficient filtering of logs.                              |
| **Real-Time Event Checking** | `.to.emit(contract, 'EventName').withArgs(...)` verifies events. |
| **Event History Retrieval**  | `queryFilter('EventName')` gets past events.                     |

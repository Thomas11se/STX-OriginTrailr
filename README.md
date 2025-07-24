
# STX-OriginTrailr

A smart contract for tracking products through a decentralized supply chain using the Clarity smart contract language on the Stacks blockchain.

---

## 📑 Overview

This smart contract manages:

* **User roles** (Manufacturer, Transporter, etc.)
* **Product registration and status**
* **Product location updates**
* **Product movement history**
* **Role revocation**

It ensures only authorized participants can interact with the supply chain and maintains a tamper-proof history of each product's journey from origin to destination.

---

## 📚 Contract Functions

### ✅ Public Functions

---

### `add-product (name, origin, location)`

Registers a new product in the system.

* **Access**: Only users with the `MANUFACTURER` role.
* **Input**:

  * `name` - Name of the product (max 100 chars)
  * `origin` - Origin of the product (max 50 chars)
  * `location` - Initial location (max 100 chars)
* **Returns**: `ok(product-id)` on success.

🔒 Fails if:

* The user is not a manufacturer.
* The input strings are invalid.
* A product with the same ID already exists.

---

### `update-location (product-id, new-location)`

Updates a product's location and logs the change to history.

* **Access**: Users with `MANUFACTURER` or `TRANSPORTER` role.
* **Input**:

  * `product-id` - ID of the product to update.
  * `new-location` - New location string (max 100 chars).
* **Returns**: `ok(change-id)` if successful.

📌 Also creates a historical record with the timestamp and current status.

---

### `revoke-role (address)`

Revokes the role of a given address (disables it).

* **Access**: Only contract owner.
* **Input**: Address of the principal to revoke.
* **Returns**: `ok` on success.

---

### `get-product (product-id)`

Fetches product data.

* **Returns**: Optional product object.

---

### `get-product-history (product-id)`

Fetches the product's history entry with `change-id: 0`.

* ⚠️ Note: Currently returns only the **first** history entry. You may want to enhance this to return the full history list.

---

### `get-role (address)`

Returns the role of a given address.

---

## 🔒 Private Functions

### `safe-get-product (product-id)`

Retrieves a product safely from the `products` map.

---

## 🗂 Data Structures

### Maps

* `products`:

  ```clarity
  {
    id: uint,
    name: string-utf8,
    manufacturer: principal,
    origin: string-utf8,
    timestamp: uint,
    current-location: string-utf8,
    status: string-utf8
  }
  ```

* `product-history`:

  ```clarity
  {
    product-id: uint,
    change-id: uint,
    timestamp: uint,
    location: string-utf8,
    status: string-utf8
  }
  ```

* `roles`:

  ```clarity
  {
    role: string-utf8,
    is-active: bool
  }
  ```

---

### Variables

* `product-counter` - Used to assign unique IDs to new products.
* `change-counter` - Used to assign unique IDs for history changes.

---

## 🚫 Error Codes

* `ERR-NOT-AUTHORIZED`: Caller lacks proper role or ownership.
* `ERR-INVALID-INPUT`: Provided strings are too long or invalid.
* `ERR-ALREADY-EXISTS`: Product with given ID already exists.
* `ERR-NOT-FOUND`: Requested product or role does not exist or is inactive.

---

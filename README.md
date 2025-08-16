<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>D-BAY Billing Manager</title>
  <meta name="description" content="D-BAY - Simple and free online billing and invoice manager">
  <meta name="keywords" content="Billing, Invoice Generator, D-BAY, Business, Free Billing Website">
  <meta name="author" content="You">
  <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/561/561127.png" />

  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      background-color: #f7f9fc;
    }
    h1 {
      color: #2d2d6b;
      text-align: center;
    }
    input, select {
      padding: 8px;
      margin: 5px 0;
      width: 100%;
      box-sizing: border-box;
    }
    .container {
      max-width: 800px;
      margin: auto;
      background: white;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 0 8px rgba(0,0,0,0.1);
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: left;
    }
    button {
      margin-top: 10px;
      padding: 10px 20px;
      background: #2d2d6b;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background: #1c1c50;
    }
    .total {
      text-align: right;
      margin-top: 10px;
      font-weight: bold;
    }
  </style>
</head>
<body>

<div class="container">
  <h1>🧾 D-BAY Billing Manager</h1>

  <label>Customer Name:</label>
  <input type="text" id="customerName" placeholder="Enter customer name" />

  <label>Item Name:</label>
  <input type="text" id="itemName" placeholder="e.g. Product A" />

  <label>Price per Unit:</label>
  <input type="number" id="itemPrice" placeholder="e.g. 100" />

  <label>Quantity:</label>
  <input type="number" id="itemQty" placeholder="e.g. 2" />

  <button onclick="addItem()">➕ Add Item</button>

  <table id="billTable">
    <thead>
      <tr>
        <th>Item</th>
        <th>Price (₹)</th>
        <th>Qty</th>
        <th>Total (₹)</th>
        <th>❌</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <div class="total" id="grandTotal">Grand Total: ₹0</div>

  <button onclick="downloadInvoice()">📤 Download Invoice</button>
</div>

<script>
  const tableBody = document.querySelector("#billTable tbody");
  let totalAmount = 0;

  function addItem() {
    const name = document.getElementById("itemName").value.trim();
    const price = parseFloat(document.getElementById("itemPrice").value);
    const qty = parseInt(document.getElementById("itemQty").value);

    if (!name || isNaN(price) || isNaN(qty)) {
      alert("Please enter valid item details.");
      return;
    }

    const row = document.createElement("tr");
    const total = price * qty;
    totalAmount += total;

    row.innerHTML = `
      <td>${name}</td>
      <td>${price}</td>
      <td>${qty}</td>
      <td>${total}</td>
      <td><button onclick="removeRow(this, ${total})">X</button></td>
    `;

    tableBody.appendChild(row);
    updateTotal();

    // Clear inputs
    document.getElementById("itemName").value = "";
    document.getElementById("itemPrice").value = "";
    document.getElementById("itemQty").value = "";
  }

  function removeRow(btn, amount) {
    totalAmount -= amount;
    btn.parentElement.parentElement.remove();
    updateTotal();
  }

  function updateTotal() {
    document.getElementById("grandTotal").innerText = `Grand Total: ₹${totalAmount.toFixed(2)}`;
  }

  function downloadInvoice() {
    const name = document.getElementById("customerName").value.trim();
    if (!name) {
      alert("Please enter the customer name.");
      return;
    }

    let content = `Invoice for: ${name}\n\n`;
    content += `Item\tPrice\tQty\tTotal\n`;

    [...tableBody.children].forEach(row => {
      const cols = row.querySelectorAll("td");
      content += `${cols[0].innerText}\t${cols[1].innerText}\t${cols[2].innerText}\t${cols[3].innerText}\n`;
    });

    content += `\nGrand Total: ₹${totalAmount.toFixed(2)}\n`;

    const blob = new Blob([content], { type: "text/plain" });
    const a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = `${name}_invoice.txt`;
    a.click();
  }
</script>

</body>
</html>

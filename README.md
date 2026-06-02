// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract InvoiceTracker {
    struct Invoice {
        uint256 id;
        address freelancer;
        uint256 amount;
        bool isPaid;
    }

    uint256 public invoiceCount;
    mapping(uint256 => Invoice) public invoices;

    event InvoiceCreated(uint256 id, address freelancer, uint256 amount);
    event PaymentReceived(uint256 id, uint256 amount);

    // Function to create a new invoice
    function createInvoice(uint256 _amount) public {
        invoiceCount++;
        invoices[invoiceCount] = Invoice(invoiceCount, msg.sender, _amount, false);
        emit InvoiceCreated(invoiceCount, msg.sender, _amount);
    }

    // Function to pay an invoice
    function payInvoice(uint256 _id) public payable {
        Invoice storage invoice = invoices[_id];
        
        require(!invoice.isPaid, "Invoice already paid");
        require(msg.value == invoice.amount, "Incorrect amount");

        invoice.isPaid = true;
        payable(invoice.freelancer).transfer(msg.value);

        emit PaymentReceived(_id, msg.value);
    }
}

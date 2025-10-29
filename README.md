# 🗳️ Transparent On-Chain Polling System

A simple and transparent **on-chain voting system** built using **Solidity**.  
This project enables users to **create polls**, **vote**, and **view real-time results** — all recorded immutably on the blockchain for full transparency.

---



<img width="1372" height="800" alt="Screenshot 2025-10-27 111338" src="https://github.com/user-attachments/assets/e23a721e-4f12-4282-82fd-5cf14e89db9e" />




## 📘 Project Description

The **Transparent On-Chain Polling System** is a beginner-friendly Solidity smart contract designed to demonstrate how decentralized polling works.  
It ensures every vote is:
- Secure ✅  
- Transparent 🧠  
- Immutable 🔒  

Poll creators can launch questions with multiple options, and participants can vote directly on-chain using their wallet. The contract prevents double voting and allows everyone to see the results.

---

## 💡 What It Does

- 🗳 **Creates polls** with multiple choice options  
- 👥 **Allows users to vote** only once per poll  
- ⏰ **Sets a duration** for polls (end time)  
- 📊 **Displays live results** transparently  
- 💬 **Emits events** for every new poll and vote for better tracking  

---

## ✨ Features

- **Anyone can create a poll** — no admin restrictions  
- **Open and transparent voting** on-chain  
- **Prevents double voting** using wallet address checks  
- **Smart contract emits events** for auditability  
- **Simple, gas-efficient, and beginner-friendly Solidity code**  

---

## 🚀 Deployed Smart Contract

**Network:** CELO (Testnet – Celo Sepolia)  
**Contract Deployment Transaction:**  
🔗 [View on BlockScout](https://celo-sepolia.blockscout.com/tx/0x6fbea569ca3a2ce06e32bf713fdbed8fb28b055d6a97025a973470301ae2b643)

---

## 🧩 Smart Contract Code

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/// @title Transparent On-Chain Polling System
/// @author ...
/// @notice A simple public polling contract where anyone can create polls and vote transparently.
contract PollSystem {
    struct Poll {
        string question;             // Poll question
        string[] options;            // Options available for voting
        mapping(uint => uint) votes; // votes[optionIndex] = voteCount
        mapping(address => bool) voted; // Prevent double voting
        uint endTime;                // Poll end time (timestamp)
        bool exists;                 // Check if poll exists
    }

    uint public pollCount;
    mapping(uint => Poll) public polls;

    event PollCreated(uint pollId, string question, uint endTime);
    event Voted(uint pollId, address voter, uint optionIndex);

    /// @notice Create a new poll with multiple options
    /// @param _question The question of the poll
    /// @param _options The list of options to choose from
    /// @param _durationSeconds Duration for the poll in seconds
    function createPoll(
        string memory _question,
        string[] memory _options,
        uint _durationSeconds
    ) public {
        require(_options.length >= 2, "Need at least 2 options");

        pollCount++;
        Poll storage p = polls[pollCount];
        p.question = _question;
        p.options = _options;
        p.endTime = block.timestamp + _durationSeconds;
        p.exists = true;

        emit PollCreated(pollCount, _question, p.endTime);
    }

    /// @notice Vote for an option in a poll
    /// @param _pollId The poll ID
    /// @param _optionIndex The index of the option to vote for
    function vote(uint _pollId, uint _optionIndex) public {
        Poll storage p = polls[_pollId];
        require(p.exists, "Poll does not exist");
        require(block.timestamp < p.endTime, "Poll has ended");
        require(!p.voted[msg.sender], "Already voted");
        require(_optionIndex < p.options.length, "Invalid option index");

        p.votes[_optionIndex]++;
        p.voted[msg.sender] = true;

        emit Voted(_pollId, msg.sender, _optionIndex);
    }

    /// @notice Get poll details (without vote counts)
    function getPoll(uint _pollId) public view returns (
        string memory question,
        string[] memory options,
        uint endTime
    ) {
        Poll storage p = polls[_pollId];
        return (p.question, p.options, p.endTime);
    }

    /// @notice Get results (vote counts) for a poll
    function getResults(uint _pollId) public view returns (uint[] memory results) {
        Poll storage p = polls[_pollId];
        uint optionCount = p.options.length;
        results = new uint[](optionCount);
        for (uint i = 0; i < optionCount; i++) {
            results[i] = p.votes[i];
        }
        return results;
    }
}
# smartcontract

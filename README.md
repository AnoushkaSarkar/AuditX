# AuditX
AuditX — README.md
markdown
# AuditX 🛡️

### AI-Powered Smart Contract Auditor — Find Vulnerabilities Before Hackers Do

![AuditX Banner](https://img.shields.io/badge/AuditX-Smart%20Contract%20Auditor-red?style=for-the-badge)
![AI](https://img.shields.io/badge/AI-GPT--4%20%2F%20Claude-412991?style=for-the-badge&logo=openai)
![Solidity](https://img.shields.io/badge/Solidity-0.8.20-363636?style=for-the-badge&logo=solidity)
![Slither](https://img.shields.io/badge/Static%20Analysis-Slither-yellow?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

> **"Your smart contract has bugs. AuditX finds them before \$5B in losses do."**

Live Demo · Audit Registry · Documentation · Polygonscan

---

## The Problem

Smart contract vulnerabilities have caused over **\$5 billion in losses**
across DeFi — and the number grows every year.

|
 Year 
|
 Exploit 
|
 Loss 
|
|
------
|
---------
|
------
|
|
 2016 
|
 The DAO — Reentrancy 
|
 \$60M 
|
|
 2021 
|
 Poly Network — Access Control 
|
 \$611M 
|
|
 2022 
|
 Ronin Bridge — Private Key 
|
 \$625M 
|
|
 2022 
|
 Wormhole — Signature Bypass 
|
 \$320M 
|
|
 2023 
|
 Euler Finance — Flash Loan 
|
 \$197M 
|

**Professional audits cost \$20,000–\$100,000 and take 4–8 weeks.**

This means:
- 🚫 Independent developers ship **unaudited contracts**
- 🚫 Hackathon teams deploy **vulnerable code** to mainnets
- 🚫 Early-stage protocols launch with **known bug patterns**
- 🚫 Students learning Solidity have **no feedback loop**

The audit industry serves the top 1% of protocols.
**AuditX serves everyone else.**

---

## The Solution

**AuditX** is an AI-powered smart contract auditing platform that combines:

- 🤖 **Large Language Model analysis** — GPT-4 / Claude explain
  vulnerabilities in plain English and suggest exact fixes
- 🔍 **Slither static analysis** — production-grade vulnerability
  detection across 90+ detectors
- ⛓️ **On-chain audit records** — optional immutable audit receipt
  stored on Polygon — publicly verifiable, tamper-proof
- 📊 **Severity scoring** — every issue rated Critical / High /
  Medium / Low with CVSS-style scoring
Paste your contract.
Get a full audit report in under 60 seconds.
For free.

text

---

## How It Works
text
 ┌─────────────────────────────────────┐
 │           DEVELOPER                 │
 │  Pastes Solidity contract or        │
 │  uploads .sol file via UI           │
 └──────────────┬──────────────────────┘
                │
                ▼
 ┌─────────────────────────────────────┐
 │         AUDITX BACKEND              │
 │         (Python / FastAPI)          │
 │                                     │
 │  ┌─────────────┐ ┌───────────────┐  │
 │  │   SLITHER   │ │  GPT-4/Claude │  │
 │  │   Static    │ │  AI Analysis  │  │
 │  │   Analysis  │ │               │  │
 │  │  90+ checks │ │  Explains     │  │
 │  │  AST parser │ │  Suggests Fix │  │
 │  └──────┬──────┘ └───────┬───────┘  │
 │         │                │          │
 │         └────────┬───────┘          │
 │                  │                  │
 │         Results Aggregated          │
 │         Severity Scored             │
 └──────────────────┬──────────────────┘
                    │
                    ▼
 ┌─────────────────────────────────────┐
 │         AUDIT REPORT                │
 │                                     │
 │  • Vulnerability list               │
 │  • Severity ratings                 │
 │  • Plain English explanations       │
 │  • Exact fix suggestions            │
 │  • Overall security score /100      │
 └──────────────────┬──────────────────┘
                    │
          Optional: Store On-Chain
                    │
                    ▼
 ┌─────────────────────────────────────┐
 │      ON-CHAIN AUDIT REGISTRY        │
 │      (Polygon Smart Contract)       │
 │                                     │
 │  Hash of (contract + report)        │
 │  stored immutably on Polygon        │
 │  Publicly verifiable audit trail    │
 └─────────────────────────────────────┘
text

---

## Vulnerability Detection

AuditX detects **all major smart contract vulnerability classes**:

### 🔴 Critical Severity

| Vulnerability | Description | Example Pattern Detected |
|--------------|-------------|--------------------------|
| **Reentrancy** | External calls before state updates allow recursive draining | `call.value()` before balance update |
| **Access Control** | Missing `onlyOwner` or role checks on sensitive functions | Unprotected `selfdestruct`, `mint` |
| **Integer Overflow** | Arithmetic wrapping in pre-0.8.0 contracts | `uint` additions without SafeMath |
| **Unchecked Call Return** | Ignoring return value of `call()` | `addr.call{value: amt}("")` unchecked |

### 🟠 High Severity

| Vulnerability | Description |
|--------------|-------------|
| **Flash Loan Attack Surface** | Price oracle manipulation via single-block liquidity |
| **Front-Running** | Transaction ordering exploitation in DEX / auction contracts |
| **Signature Replay** | Missing nonce in signed message verification |
| **Delegatecall Injection** | Unsafe delegatecall to user-controlled addresses |

### 🟡 Medium Severity

| Vulnerability | Description |
|--------------|-------------|
| **Timestamp Dependence** | Using `block.timestamp` for randomness or time locks |
| **Tx.Origin Authentication** | Using `tx.origin` instead of `msg.sender` |
| **Uninitialized Storage** | Storage pointer pointing to unexpected slot |
| **DoS via Gas Limit** | Unbounded loops over user-controlled arrays |

### 🔵 Low / Informational
→ Missing event emissions on state changes
→ Floating pragma versions
→ Unused variables / dead code
→ Missing zero-address checks
→ Centralization risks (single owner)
→ SPDX license identifier missing

text

---

## AI Analysis — What It Looks Like

### Input Contract (Vulnerable)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VulnerableVault {
    mapping(address => uint256) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() public {
        uint256 amount = balances[msg.sender];
        require(amount > 0, "No balance");

        // 🔴 CRITICAL: External call BEFORE state update
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success, "Transfer failed");

        balances[msg.sender] = 0;  // Too late — reentrancy possible
    }
}
AuditX Output
json
{
  "auditId": "0x7f3a...c912",
  "contract": "VulnerableVault.sol",
  "overallScore": 23,
  "grade": "F",
  "timestamp": "2024-01-15T10:23:45Z",
  "summary": {
    "critical": 1,
    "high": 0,
    "medium": 1,
    "low": 2,
    "informational": 1
  },
  "vulnerabilities": [
    {
      "id": "VULN-001",
      "name": "Reentrancy Attack",
      "severity": "CRITICAL",
      "cvssScore": 9.8,
      "line": 14,
      "function": "withdraw()",
      "description": "The withdraw() function sends ETH to msg.sender
        via a low-level call BEFORE updating the balance to zero.
        A malicious contract can re-enter withdraw() recursively in
        its fallback function, draining the entire vault before
        balances[msg.sender] is ever set to 0.",
      "attackScenario": "1. Attacker deploys MaliciousContract with 
        a fallback that calls vault.withdraw() again.
        2. Attacker deposits 1 ETH and calls withdraw().
        3. Vault sends 1 ETH — attacker fallback re-enters.
        4. Balance still shows 1 ETH — vault sends again.
        5. Repeat until vault is drained.",
      "recommendation": "Apply the Checks-Effects-Interactions pattern.
        Update balances[msg.sender] = 0 BEFORE the external call.
        Alternatively use OpenZeppelin ReentrancyGuard.",
      "fixedCode": "function withdraw() public nonReentrant {\n
        uint256 amount = balances[msg.sender];\n
        require(amount > 0, 'No balance');\n
        balances[msg.sender] = 0;  // State update FIRST\n
        (bool success, ) = msg.sender.call{value: amount}('');\n
        require(success, 'Transfer failed');\n}",
      "references": [
        "https://swcregistry.io/docs/SWC-107",
        "https://consensys.github.io/smart-contract-best-practices/attacks/reentrancy"
      ]
    },
    {
      "id": "VULN-002",
      "name": "Missing Access Control",
      "severity": "MEDIUM",
      "line": 0,
      "description": "Contract has no owner or admin controls.
        No ability to pause in case of emergency.",
      "recommendation": "Implement OpenZeppelin Ownable and Pausable
        for emergency circuit breaker functionality."
    }
  ]
}
Tech Stack
Layer	Technology
AI Engine	GPT-4 / Claude 3.5 Sonnet API
Static Analysis	Slither (Trail of Bits)
Backend	Python 3.11, FastAPI
Smart Contracts	Solidity 0.8.20
Blockchain	Polygon PoS
Frontend	React.js, TailwindCSS
Wallet	MetaMask, Ethers.js
Storage	IPFS (full audit report)
Database	PostgreSQL (audit history)
Deployment	Vercel (frontend), Railway (backend)
Testing	Pytest, Hardhat
Project Structure
text

AuditX/
├── backend/
│   ├── main.py                    # FastAPI app entry point
│   ├── routers/
│   │   ├── audit.py               # POST /audit endpoint
│   │   └── registry.py            # GET /audit/{id} endpoint
│   ├── services/
│   │   ├── slither_service.py     # Slither static analysis runner
│   │   ├── ai_service.py          # GPT-4 / Claude integration
│   │   ├── aggregator.py          # Merge + deduplicate findings
│   │   └── scorer.py              # CVSS severity scoring
│   ├── models/
│   │   ├── audit_report.py        # Pydantic models
│   │   └── vulnerability.py
│   └── requirements.txt
│
├── contracts/
│   ├── AuditRegistry.sol          # On-chain audit record storage
│   └── interfaces/
│       └── IAuditRegistry.sol
│
├── scripts/
│   └── deploy.js                  # Hardhat deployment
│
├── test/
│   ├── test_slither.py            # Slither service tests
│   ├── test_ai_service.py         # AI analysis tests
│   └── AuditRegistry.test.js     # Contract tests
│
├── src/
│   ├── components/
│   │   ├── ConnectWallet.jsx
│   │   ├── ContractUploader.jsx   # Paste or upload .sol file
│   │   ├── AuditReport.jsx        # Full report display
│   │   ├── VulnerabilityCard.jsx  # Per-vulnerability breakdown
│   │   ├── SecurityScore.jsx      # Score gauge /100
│   │   ├── FixSuggestion.jsx      # AI-generated fix with diff view
│   │   └── RegisterOnChain.jsx    # Store audit on Polygon
│   ├── utils/
│   │   ├── api.js                 # Backend API calls
│   │   └── contract.js            # Ethers.js registry interaction
│   └── App.jsx
│
├── hardhat.config.js
├── package.json
└── README.md
Smart Contract — Audit Registry
solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title AuditX Registry
/// @notice Immutable on-chain record of smart contract audits
/// @dev Stores hash of (contract bytecode + audit report) on Polygon

contract AuditRegistry {

    struct AuditRecord {
        bytes32 contractHash;    // keccak256 of contract source
        bytes32 reportHash;      // keccak256 of full audit report
        address auditor;         // Wallet that submitted audit
        uint256 timestamp;       // Block timestamp
        uint8   overallScore;    // Security score out of 100
        string  ipfsCID;         // Full report on IPFS
        bool    criticalFound;   // True if critical vulns exist
    }

    // auditId => AuditRecord
    mapping(bytes32 => AuditRecord) public audits;

    // contractHash => latest auditId (for quick lookup)
    mapping(bytes32 => bytes32) public latestAudit;

    // Total audits performed
    uint256 public totalAudits;

    event AuditRecorded(
        bytes32 indexed auditId,
        bytes32 indexed contractHash,
        address indexed auditor,
        uint8   score,
        bool    criticalFound,
        string  ipfsCID
    );

    /// @notice Record an audit result on-chain
    function recordAudit(
        bytes32 _contractHash,
        bytes32 _reportHash,
        uint8   _score,
        bool    _criticalFound,
        string  calldata _ipfsCID
    ) external returns (bytes32 auditId) {

        auditId = keccak256(abi.encodePacked(
            _contractHash,
            _reportHash,
            msg.sender,
            block.timestamp
        ));

        audits[auditId] = AuditRecord({
            contractHash  : _contractHash,
            reportHash    : _reportHash,
            auditor       : msg.sender,
            timestamp     : block.timestamp,
            overallScore  : _score,
            ipfsCID       : _ipfsCID,
            criticalFound : _criticalFound
        });

        latestAudit[_contractHash] = auditId;
        totalAudits++;

        emit AuditRecorded(
            auditId,
            _contractHash,
            msg.sender,
            _score,
            _criticalFound,
            _ipfsCID
        );

        return auditId;
    }

    /// @notice Verify a contract's latest audit result
    function verifyContract(
        bytes32 _contractHash
    ) external view returns (
        bool    hasAudit,
        uint8   score,
        bool    criticalFound,
        uint256 auditedAt,
        string  memory ipfsCID
    ) {
        bytes32 auditId = latestAudit[_contractHash];
        if (auditId == bytes32(0)) return (false, 0, false, 0, "");

        AuditRecord memory record = audits[auditId];
        return (
            true,
            record.overallScore,
            record.criticalFound,
            record.timestamp,
            record.ipfsCID
        );
    }
}
Backend — Core Services
AI Analysis Service
python
# services/ai_service.py

import openai
from models.audit_report import Vulnerability, Severity

SYSTEM_PROMPT = """
You are an expert Solidity smart contract security auditor with 10+ years 
of experience. You have deep knowledge of:
- All SWC (Smart Contract Weakness Classification) registry entries
- EVM bytecode and Solidity compiler behavior
- DeFi attack patterns (flash loans, oracle manipulation, MEV)
- OpenZeppelin security standards

When analyzing a contract you must:
1. Identify ALL vulnerabilities — no matter how minor
2. Rate each by severity: CRITICAL / HIGH / MEDIUM / LOW
3. Explain the attack scenario in plain English
4. Provide the exact fixed code
5. Reference SWC registry IDs where applicable
6. Return ONLY valid JSON matching the provided schema
"""

async def analyze_with_ai(contract_source: str) -> list[Vulnerability]:
    
    client = openai.AsyncOpenAI()
    
    response = await client.chat.completions.create(
        model="gpt-4-turbo-preview",
        messages=[
            {"role": "system", "content": SYSTEM_PROMPT},
            {"role": "user",   "content": f"""
                Audit this Solidity smart contract.
                Return a JSON array of vulnerability objects.
                
                Contract:
                ```solidity
                {contract_source}
                ```
                
                Return format:
                {{
                  "vulnerabilities": [
                    {{
                      "name": string,
                      "severity": "CRITICAL|HIGH|MEDIUM|LOW|INFO",
                      "cvssScore": float,
                      "line": int,
                      "function": string,
                      "description": string,
                      "attackScenario": string,
                      "recommendation": string,
                      "fixedCode": string,
                      "swcId": string
                    }}
                  ],
                  "overallScore": int,
                  "summary": string
                }}
            """}
        ],
        response_format={"type": "json_object"},
        temperature=0.1   # Low temp = consistent, deterministic output
    )
    
    return parse_ai_response(response.choices[0].message.content)
Slither Static Analysis Service
python
# services/slither_service.py

import subprocess
import tempfile
import json
import os
from models.vulnerability import SlitherFinding

async def run_slither(contract_source: str) -> list[SlitherFinding]:
    
    # Write contract to temp file
    with tempfile.NamedTemporaryFile(
        suffix=".sol", 
        mode="w", 
        delete=False
    ) as f:
        f.write(contract_source)
        temp_path = f.name

    try:
        # Run Slither with JSON output
        result = subprocess.run(
            [
                "slither", temp_path,
                "--json", "-",
                "--exclude-dependencies",
                "--solc-remaps", "@openzeppelin=node_modules/@openzeppelin"
            ],
            capture_output=True,
            text=True,
            timeout=60
        )

        raw = json.loads(result.stdout)
        return parse_slither_output(raw)

    except subprocess.TimeoutExpired:
        return [SlitherFinding(
            name="Analysis Timeout",
            severity="INFO",
            description="Contract too large for static analysis timeout"
        )]
    finally:
        os.unlink(temp_path)


def parse_slither_output(raw: dict) -> list[SlitherFinding]:
    findings = []
    
    for result in raw.get("results", {}).get("detectors", []):
        findings.append(SlitherFinding(
            name        = result["check"],
            severity    = map_slither_severity(result["impact"]),
            confidence  = result["confidence"],
            description = result["description"],
            lines       = extract_lines(result["elements"]),
            swcId       = SLITHER_TO_SWC.get(result["check"], "")
        ))
    
    return findings


# Slither impact → AuditX severity mapping
def map_slither_severity(impact: str) -> str:
    mapping = {
        "High"          : "CRITICAL",
        "Medium"        : "HIGH",
        "Low"           : "MEDIUM",
        "Informational" : "LOW",
        "Optimization"  : "INFO"
    }
    return mapping.get(impact, "INFO")
Results Aggregator
python
# services/aggregator.py

from services.slither_service import run_slither
from services.ai_service      import analyze_with_ai
from services.scorer          import calculate_score

async def full_audit(contract_source: str) -> dict:

    # Run both analyses in parallel
    import asyncio
    slither_findings, ai_findings = await asyncio.gather(
        run_slither(contract_source),
        analyze_with_ai(contract_source)
    )

    # Merge and deduplicate
    all_findings = merge_findings(slither_findings, ai_findings)

    # Calculate overall security score
    score = calculate_score(all_findings)

    return {
        "vulnerabilities" : all_findings,
        "overallScore"    : score,
        "grade"           : score_to_grade(score),
        "summary"         : {
            "critical"      : count_by_severity(all_findings, "CRITICAL"),
            "high"          : count_by_severity(all_findings, "HIGH"),
            "medium"        : count_by_severity(all_findings, "MEDIUM"),
            "low"           : count_by_severity(all_findings, "LOW"),
            "informational" : count_by_severity(all_findings, "INFO"),
        }
    }


def score_to_grade(score: int) -> str:
    if score >= 90: return "A"
    if score >= 75: return "B"
    if score >= 60: return "C"
    if score >= 40: return "D"
    return "F"
API Reference
POST /audit
Submit a contract for analysis

Request

json
{
  "contractSource": "// SPDX-License-Identifier: MIT\npragma solidity...",
  "contractName": "VulnerableVault",
  "storeOnChain": true
}
Response

json
{
  "auditId": "0x7f3a...c912",
  "contractName": "VulnerableVault",
  "overallScore": 23,
  "grade": "F",
  "duration": "12.4s",
  "summary": {
    "critical": 1,
    "high": 0,
    "medium": 1,
    "low": 2,
    "informational": 1
  },
  "vulnerabilities": [...],
  "ipfsCID": "QmX7...",
  "polygonscanTx": "0x9a2b..."
}
GET /audit/{auditId}
Retrieve a previous audit report

GET /verify/{contractHash}
Check if a contract has an on-chain audit record

GET /stats
Platform-wide audit statistics

Getting Started
Prerequisites
bash
Node.js v18+
Python 3.11+
MetaMask browser extension
Slither (Trail of Bits)
OpenAI API key or Anthropic API key
Installation
bash
# Clone the repository
git clone https://github.com/AnoushkaSarkar/AuditX.git
cd AuditX
Backend Setup
bash
# Navigate to backend
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Install Slither
pip install slither-analyzer

# Install Solidity compiler
pip install solc-select
solc-select install 0.8.20
solc-select use 0.8.20

# Set environment variables
cp .env.example .env
# Add to .env:
# OPENAI_API_KEY=sk-...
# ANTHROPIC_API_KEY=sk-ant-...
# DATABASE_URL=postgresql://...
# POLYGON_RPC_URL=...
# PRIVATE_KEY=...

# Start backend
uvicorn main:app --reload --port 8000
Smart Contract Deployment
bash
# Install dependencies
npm install

# Deploy to Polygon Mumbai testnet
npx hardhat run scripts/deploy.js --network polygonMumbai
Frontend Setup
bash
# Navigate to frontend
cd src

# Install dependencies
npm install

# Start development server
npm start
Open http://localhost:3000

Running Tests
bash
# Backend tests
cd backend
pytest tests/ -v --cov=services

# Smart contract tests
npx hardhat test

# Full coverage report
npx hardhat coverage
Test Results
text
Backend Services
  Slither Service
    ✔ Detects reentrancy in vulnerable contract       (1.2s)
    ✔ Detects missing access control                  (0.9s)
    ✔ Detects integer overflow (pre-0.8.0)            (1.1s)
    ✔ Returns empty array for clean contract          (0.8s)
    ✔ Handles analysis timeout gracefully             (0.3s)
    
  AI Service
    ✔ Returns valid vulnerability JSON                (3.1s)
    ✔ Identifies reentrancy with attack scenario      (3.4s)
    ✔ Generates correct fix for reentrancy            (3.2s)
    ✔ Handles malformed Solidity gracefully           (2.8s)

  Aggregator
    ✔ Merges Slither and AI findings correctly        (0.1s)
    ✔ Deduplicates overlapping vulnerabilities        (0.1s)
    ✔ Calculates correct severity score               (0.1s)

AuditRegistry Contract
  ✔ Deploys with correct initial state
  ✔ Records audit and emits event
  ✔ Returns correct audit by ID
  ✔ Returns latest audit for contract hash
  ✔ verifyContract returns false for unknown hash
  ✔ Increments totalAudits correctly

18 passing (14.2s)
Contract Addresses
Network	Contract	Address
Polygon Mumbai	AuditRegistry	0x...
Polygon Mainnet	AuditRegistry	0x...
Key Design Decisions
Why Both Slither AND an LLM?
text
Slither alone:
✅ Fast, deterministic, no hallucinations
❌ No explanations, no fix suggestions, no context

LLM alone:
✅ Rich explanations, attack scenarios, fix code
❌ Can hallucinate, misses pattern-level bugs

Together:
✅ Slither catches what it's designed to catch (deterministic)
✅ LLM explains, contextualizes, and fixes (intelligent)
✅ Cross-validation reduces false positives
✅ Best of both worlds
Why On-Chain Audit Records?
Creates a public, tamper-proof audit history for any contract
Developers can prove their contract was audited
Users can verify audit status before interacting with a protocol
Immutable timestamp = proof of audit date
Why Polygon?
Sub-₹1 gas fees make storing audit records economically viable
Fast finality — audit record confirmed in seconds
EVM compatible — full Solidity + Hardhat tooling
Security Considerations
text
✅ Contract source code is NOT stored — only cryptographic hashes
✅ AI responses validated against strict JSON schema
✅ Rate limiting on /audit endpoint (10 req/min per IP)
✅ Slither runs in isolated subprocess with timeout
✅ No user authentication required — fully permissionless
⚠️  LLM output is informational — not a substitute for professional audit
⚠️  OpenAI/Anthropic API subject to availability
⚠️  AuditRegistry contract pending formal security review
Roadmap
text
Phase 1 — MVP (Current)
├── ✅ Slither static analysis integration
├── ✅ GPT-4 AI vulnerability analysis
├── ✅ Severity scoring and grading
├── ✅ On-chain audit registry (Polygon)
├── ✅ IPFS full report storage
└── ✅ React frontend with MetaMask

Phase 2 — Intelligence
├── [ ] Fine-tuned model on SWC vulnerability dataset
├── [ ] Vyper language support
├── [ ] Historical exploit pattern matching
├── [ ] Multi-file / multi-contract project support
└── [ ] GitHub PR integration (audit on every push)

Phase 3 — Platform
├── [ ] Audit comparison (before vs after fix)
├── [ ] Public audit leaderboard
├── [ ] Developer reputation scores
├── [ ] API for CI/CD pipeline integration
└── [ ] VS Code extension

Phase 4 — Ecosystem
├── [ ] Community auditor staking + rewards
├── [ ] Formal verification integration (Certora)
├── [ ] Insurance protocol integration
└── [ ] DAO governance for vulnerability database
Why AuditX Matters
text
\$5B+ lost to smart contract exploits
        ↓
Professional audits cost \$20K–\$100K
        ↓
Independent developers ship unaudited code
        ↓
Users lose funds to preventable bugs
        ↓
AuditX makes professional-grade auditing
accessible to every developer for free
        ↓
Safer contracts. Safer DeFi. Safer users.
AuditX doesn't replace professional auditors.
It makes sure no developer ever ships an
obviously vulnerable contract again.

Built With
Slither — Trail of Bits static analyzer
OpenAI GPT-4 — AI vulnerability analysis
Anthropic Claude — Alternative AI engine
OpenZeppelin — Smart contract standards
Polygon — On-chain audit registry
IPFS — Decentralized report storage
FastAPI — Python backend framework
Hardhat — Contract development + testing
Author
Anoushka Sarkar
GitHub · LinkedIn · Twitter

Disclaimer
AuditX is a developer assistance tool. Reports generated by AuditX
are informational only and do not constitute a formal professional
security audit. Always engage a certified auditor before deploying
contracts managing significant funds.

License
This project is open source and available under the
MIT License.

Acknowledgements
Trail of Bits for Slither
SWC Registry for vulnerability classification
ConsenSys Diligence for security research
The open source smart contract security community
*AuditX — Because the next $600M exploit is in someone

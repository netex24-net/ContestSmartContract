# ContestSmartContract
SmartContract для проведения розыгрыша

    // SPDX-License-Identifier: MIT
    pragma solidity >=0.7.0 <0.9.0;

    contract ReviewsContest {
        address private owner;
    
    constructor() {
        owner = msg.sender;
    }
    
    function playout(uint256 requiredWinnersCount, uint256[] memory ids, bytes32[] memory identifiers) public {
        require(msg.sender == owner, "Caller is not owner");
        require(identifiers.length >= requiredWinnersCount, "Participants count should be equal or greater than required winners count");
        require(requiredWinnersCount > 0 && identifiers.length > 0, "Pariticipants and requried winners count should be greater than 0");
        
        uint256 iterator = 0;
        uint256 currentWinnersCount = 0;
        bool[] memory winners = new bool[](identifiers.length);
        
        while (currentWinnersCount < requiredWinnersCount){
             bytes memory encoded = abi.encodePacked(
                identifiers,
                currentWinnersCount,
                iterator,
                block.timestamp,
                blockhash(block.number - 1),
                block.coinbase);
            iterator++;
            uint256 randomNumber = uint256(keccak256(encoded)) % identifiers.length;
            if(winners[randomNumber])
            {
                continue;
            }
            
            winners[randomNumber] = true;
            emit  Win(ids[randomNumber], identifiers[randomNumber]);
            currentWinnersCount++;
        }
    }
    
    event Win(uint256 id, bytes32 identifier);
}



ABI 

[
    {
        "inputs": [],
        "stateMutability": "nonpayable",
        "type": "constructor"
    },
    {
        "anonymous": false,
        "inputs": [
            {
                "indexed": false,
                "internalType": "uint256",
                "name": "id",
                "type": "uint256"
            },
            {
                "indexed": false,
                "internalType": "bytes32",
                "name": "identifier",
                "type": "bytes32"
            }
        ],
        "name": "Win",
        "type": "event"
    },
    {
        "inputs": [
            {
                "internalType": "uint256",
                "name": "requiredWinnersCount",
                "type": "uint256"
            },
            {
                "internalType": "uint256[]",
                "name": "ids",
                "type": "uint256[]"
            },
            {
                "internalType": "bytes32[]",
                "name": "identifiers",
                "type": "bytes32[]"
            }
        ],
        "name": "playout",
        "outputs": [],
        "stateMutability": "nonpayable",
        "type": "function"
    }
]

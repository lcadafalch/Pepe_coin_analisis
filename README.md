# Pepe_coin_analisis
![Screenshot](pepecoin.jfif)


## Iniciativa
Descripción del smart-contract de Pepecoin y ver si es fiable o tiene a nivel de código un posible scam o rugpull.
Pepe

## Descripción 
Análisis del Smart-Contract de PepeCoin.
Dirección del contrato : 0x6982508145454Ce325dDbE47a25d4ec3d2311933
Dirección del contrato en etherscan : https://etherscan.io/token/0x6982508145454Ce325dDbE47a25d4ec3d2311933

## Definición 
El token  de $PEPE es un meme coin , que está basado en un meme de internet, que está en la blockchain de Ethereum, el token en si no tiene ninuna intención ningún tipo de valor, simplemente sigue la senda de DogeCoin, o Shiba inu.

## Estructura
Pepecoin es un token en la red de Ethereum, su estructura inicial es un ERC20 y sigue la estructura misma

## Fase de minting / Ejectución
En la primera fase de un smart contract se ejecuta el constructor
```solidity
    constructor(uint256 _totalSupply) ERC20("Pepe", "PEPE") {
        _mint(msg.sender, _totalSupply);
    }
```

La primera parte que se ejecuta de un smart contract es el constructor, en este caso el constructor manda los tokens a su propia wallet

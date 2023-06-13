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

La primera parte que se ejecuta de un smart contract es el constructor, en este caso el constructor manda los tokens a su propia wallet.

## Distribución
En esta segunda fase, de distribucón de tokens, ni airdrop , simplemente el propietario ha enviado los tokens en una liquidity pool, en esta liquidity pool se envia un poco más del 90% de los tokens, más 2 ether para conformar la liquidity pool
Visualizar en: https://etherscan.io/token/0x6982508145454Ce325dDbE47a25d4ec3d2311933

## Transfer
 En la función transfer  podemos ver que se requiere que la cartera no esté blacklisted con lo que significa que el control del equipo de desarrollo de pepecoin tiene potestad para poder editar que cartera puede comprar y cuál no.
 ```solidity 
    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) override internal virtual {
        require(!blacklists[to] && !blacklists[from], "Blacklisted");

        if (uniswapV2Pair == address(0)) {
            require(from == owner() || to == owner(), "trading is not started");
            return;
        }
  ```
  Pero en su momento el equipo de desarrolladores ejecutó la función para dejar de ser propietarios de manera que dejaron de controlar el smartcontract , y ya era totalmente descentralizado
 ```solidity
     function renounceOwnership() public virtual onlyOwner {
        _transferOwnership(address(0));
    }
```
## Mínimos y máximos de holding 
En este caso esta función está fuera de funcionamiento porque el propietario del token resignó al ownership, pero era para controlar mínimos y máximos que podía controlar cada usuario por cartera, posiblemente un sistema de control de ballenas.


```solidity 
    function setRule(bool _limited, address _uniswapV2Pair, uint256 _maxHoldingAmount, uint256 _minHoldingAmount) external onlyOwner {
        limited = _limited;
        uniswapV2Pair = _uniswapV2Pair;
        maxHoldingAmount = _maxHoldingAmount;
        minHoldingAmount = _minHoldingAmount;
    }

    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) override internal virtual {
        require(!blacklists[to] && !blacklists[from], "Blacklisted");

        if (uniswapV2Pair == address(0)) {
            require(from == owner() || to == owner(), "trading is not started");
            return;
        }

        if (limited && from == uniswapV2Pair) {
            require(super.balanceOf(to) + amount <= maxHoldingAmount && super.balanceOf(to) + amount >= minHoldingAmount, "Forbid");
        }
    }
  ```

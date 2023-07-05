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
## Descripción detallada del contrato

La primera parte hay toda la parte de importaciones, la licencia, el uso del framework de hardhat y openzeppelin 
```solidity

// Sources flattened with hardhat v2.7.0 https://hardhat.org
// File @openzeppelin/contracts/utils/Context.sol@v4.4.0
// SPDX-License-Identifier: MIT
// OpenZeppelin Contracts v4.4.0 (utils/Context.sol)
```
Sigue con el uso de la función context que **Context.sol** es un contrato inteligente de la biblioteca de contratos OpenZeppelin. Proporciona una implementación básica de un contexto de ejecución para otros contratos. El contexto de ejecución se refiere a la información disponible durante la ejecución de un contrato, como la dirección del remitente o la identificación del contrato.

La finalidad principal de Context.sol es proporcionar una forma estándar de acceder a la dirección del remitente (la cuenta o contrato que inició la transacción) y otros valores de contexto importantes. Esto es útil para muchos contratos, ya que a menudo necesitan saber quién es el remitente para tomar decisiones o realizar ciertas operaciones.


### Primer contrato referente a la importación de Context.sol (openzeppelin)
```solidity
abstract contract Context {
    function _msgSender() internal view virtual returns (address) {
        return msg.sender;
    }

    function _msgData() internal view virtual returns (bytes calldata) {
        return msg.data;
    }
}
```

El propósito de este contrato "Context" es proporcionar información básica de contexto a los contratos que heredan de él. Define dos funciones internas de vista: "_msgSender()" y "_msgData()".

La función "_msgSender()" devuelve la dirección de la cuenta que inició la transacción actual (es decir, el remitente del mensaje). "msg.sender" es una variable global en Solidity que representa el remitente del mensaje o transacción actual.

La función "_msgData()" devuelve los datos crudos de la llamada a la función actual como una matriz de bytes. "msg.data" es otra variable global que contiene los datos completos de la llamada a la función actual.

Ambas funciones están marcadas como "internal", lo que significa que solo se pueden llamar desde dentro del contrato en sí y sus contratos derivados. También están marcadas como "virtual", lo que indica que pueden ser sobrescritas por los contratos que heredan de este contrato "Context".

### Segundo contrato referente al Ownership (openzeppelin)
Este contrato solo trata temas de manejo y visualización del ownership a través del estándar de openzeppelin
```solidity
abstract contract Ownable is Context {
    address private _owner;
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
    constructor() {
        _transferOwnership(_msgSender());
    }
    function owner() public view virtual returns (address) {
        return _owner;
    }
    modifier onlyOwner() {
        require(owner() == _msgSender(), "Ownable: caller is not the owner");
        _;
    }
    function renounceOwnership() public virtual onlyOwner {
        _transferOwnership(address(0));
    }

```
## Tercer contrato / IERC20 estándar de Ethereum ( openzeppelin)

El tercer contrato parte otra vez desde Openzeppelin, este caso es la interficie para crear , modificar, y usar un token en la red de ethereum, cómo son las anteriores, parte de el estándar creado por Openzeppelin.

Básicamente hay temas de **transferencia**, cantidad **total** y **prestación** y eventos para la visualización de las ** Transferencias **
*/
```solidity
interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address recipient, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(
        address sender,
        address recipient,
        uint256 amount
    ) external returns (bool);
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

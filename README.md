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
### Tercer contrato / IERC20 estándar de Ethereum ( openzeppelin)

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

### Cuarto contrato / Iplementación de la interface y metadatos de los ERC20

El código siguente específicamente una interfaz llamada IERC20Metadata. Esta interfaz define una serie de funciones que proporcionan información sobre un token ERC-20 (Ethereum Request for Comment 20).

Las interfaces en Solidity son como contratos incompletos que solo contienen declaraciones de funciones y eventos, sin incluir su implementación. Sirven para establecer una comunicación estandarizada entre diferentes contratos, permitiendo que interactúen correctamente.

En este caso, la interfaz IERC20Metadata hereda de otra interfaz llamada IERC20. Esto significa que IERC20Metadata incluye todas las funciones definidas en IERC20, además de las que se definen en esta interfaz.

A continuación, describiré las funciones que se definen en la interfaz IERC20Metadata:

name() external view returns (string memory): Esta función devuelve el nombre del token ERC-20, representado como una cadena de texto (string).

symbol() external view returns (string memory): Esta función devuelve el símbolo del token ERC-20, también representado como una cadena de texto (string). El símbolo suele ser una abreviatura o código corto que representa el token, como "ETH" para Ether o "BTC" para Bitcoin.

decimals() external view returns (uint8): Esta función devuelve la cantidad de decimales que se utilizan para representar las fracciones del token. Por ejemplo, si decimals devuelve 18, significa que el token tiene 18 decimales y la unidad más pequeña es 1 wei. Si decimals devuelve 8, la unidad más pequeña sería 1 satoshi.

En cuanto al comentario que menciona @openzeppelin/contracts/token/ERC20/ERC20.sol@v4.4.0, indica que esta interfaz es parte de los contratos de OpenZeppelin, una biblioteca de contratos inteligentes desarrollada para facilitar el desarrollo seguro y estandarizado de aplicaciones descentralizadas (DApps) en la red Ethereum.

Cabe destacar que esta es solo una parte de un contrato más grande, y normalmente se utilizará junto con la implementación completa del contrato ERC-20 que contiene las funciones definidas en esta interfaz. Además, el contrato completo de IERC20Metadata también debe heredar la implementación de las funciones de la interfaz IERC20.
```solidity
interface IERC20Metadata is IERC20 {
    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint8);
}
```
### Quinto contrato // Creación del contrato ERC20
El contrato de a continuación representa una implementación de un ERC-20, el estándar para crear tokens fungíbles en la red de Ethereum.
```solidity
pragma solidity ^0.8.0;

contract ERC20 is Context, IERC20, IERC20Metadata {
    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    uint256 private _totalSupply;
    string private _name;
    string private _symbol;
```
Las variables principales son:
<br/>
**_balances** mapping que controla la cantidad de de tokens de cada cartera.
<br/>
**_allowances** mapping que visualiza la cantidad de tokens gastados por cada cartera usado por la función approve y transferForm
<br/>
**_totalSupply** uint que representa el total de tokens en circulación
<br/>
**name** string que representa el nombre del token.
<br/>
**_symbol** string que representa el simbolo del token.

``` solidity
    constructor(string memory name_, string memory symbol_) {
        _name = name_;
        _symbol = symbol_;
    }
```
El constructor se inicializa con el nombre y el símbolo que le asignamos , recordemos que el constructor se ejecuta sólo una vez, cuando se ejecuta el contrato por primera vez.
 
```solidity
    function name() public view virtual override returns (string memory) {
        return _name;
    }
```
**name():** Devuelve el nombre del token como una cadena de texto, sólo la string, la palabra reservada view sólo es para visualizar.

```solidity
    function symbol() public view virtual override returns (string memory) {
        return _symbol;
    }
```
symbol(): Devuelve el símbolo del token como una cadena de texto.
```solidity
    function decimals() public view virtual override returns (uint8) {
        return 18;
    }
```
decimals(): Devuelve la cantidad de decimales que el token utiliza para su representación. En este caso, siempre devuelve 18, lo que es común en muchos tokens de la red de Ethereum

```solidity
    function totalSupply() public view virtual override returns (uint256) {
        return _totalSupply;
    }
```
totalSupply(): Devuelve el total de unidades del token en existencia.

```solidity
    function balanceOf(address account) public view virtual override returns (uint256) {
        return _balances[account];
    }
```
balanceOf(address account): Devuelve el saldo del token del titular de la cuenta especificada.
```solidity
    function transfer(address recipient, uint256 amount) public virtual override returns (bool) {
        _transfer(_msgSender(), recipient, amount);
        return true;
    }

    function allowance(address owner, address spender) public view virtual override returns (uint256) {
        return _allowances[owner][spender];
    }
    function approve(address spender, uint256 amount) public virtual override returns (bool) {
        _approve(_msgSender(), spender, amount);
        return true;
    }
    function transferFrom(
        address sender,
        address recipient,
        uint256 amount
    ) public virtual override returns (bool) {
        _transfer(sender, recipient, amount);

        uint256 currentAllowance = _allowances[sender][_msgSender()];
        require(currentAllowance >= amount, "ERC20: transfer amount exceeds allowance");
        unchecked {
            _approve(sender, _msgSender(), currentAllowance - amount);
        }

        return true;
    }
    function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) {
        _approve(_msgSender(), spender, _allowances[_msgSender()][spender] + addedValue);
        return true;
    }
    function decreaseAllowance(address spender, uint256 subtractedValue) public virtual returns (bool) {
        uint256 currentAllowance = _allowances[_msgSender()][spender];
        require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
        unchecked {
            _approve(_msgSender(), spender, currentAllowance - subtractedValue);
        }

        return true;
    }
    function _transfer(
        address sender,
        address recipient,
        uint256 amount
    ) internal virtual {
        require(sender != address(0), "ERC20: transfer from the zero address");
        require(recipient != address(0), "ERC20: transfer to the zero address");

        _beforeTokenTransfer(sender, recipient, amount);

        uint256 senderBalance = _balances[sender];
        require(senderBalance >= amount, "ERC20: transfer amount exceeds balance");
        unchecked {
            _balances[sender] = senderBalance - amount;
        }
        _balances[recipient] += amount;

        emit Transfer(sender, recipient, amount);

        _afterTokenTransfer(sender, recipient, amount);
    }
    function _mint(address account, uint256 amount) internal virtual {
        require(account != address(0), "ERC20: mint to the zero address");

        _beforeTokenTransfer(address(0), account, amount);

        _totalSupply += amount;
        _balances[account] += amount;
        emit Transfer(address(0), account, amount);

        _afterTokenTransfer(address(0), account, amount);
    }
    function _burn(address account, uint256 amount) internal virtual {
        require(account != address(0), "ERC20: burn from the zero address");

        _beforeTokenTransfer(account, address(0), amount);

        uint256 accountBalance = _balances[account];
        require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
        unchecked {
            _balances[account] = accountBalance - amount;
        }
        _totalSupply -= amount;

        emit Transfer(account, address(0), amount);

        _afterTokenTransfer(account, address(0), amount);
    }
    function _approve(
        address owner,
        address spender,
        uint256 amount
    ) internal virtual {
        require(owner != address(0), "ERC20: approve from the zero address");
        require(spender != address(0), "ERC20: approve to the zero address");

        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }
    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) internal virtual {}
    function _afterTokenTransfer(
        address from,
        address to,
        uint256 amount
    ) internal virtual {}
}
```


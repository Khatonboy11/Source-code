
// File: contracts/VRC25Permit.sol

pragma solidity >= 0.6.2;

// Sources flattened with hardhat v2.12.6 https://hardhat.org

// File contracts/interfaces/IERC165.sol

/**
 * @dev Interface of the ERC165 standard, as defined in the
 * https://eips.ethereum.org/EIPS/eip-165[EIP].
 *
 * Implementers can declare support of contract interfaces, which can then be
 * queried by others ({ERC165Checker}).
 *
 * For an implementation, see {ERC165}.
 */
interface IERC165 {
    function supportsInterface(bytes4 interfaceId) external view returns(bool);
}


// File contracts/interfaces/IVRC25.sol

/**
 * @dev Interface of the VRC25 standard as defined in the EIP.
 */
interface IVRC25 {
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event Fee(address indexed from, address indexed to, address indexed issuer, uint256 value);

function decimals() external view returns(uint8);

/**
 * @notice Returns the amount of tokens in existence.
 */
function totalSupply() external view returns(uint256);

function balanceOf(address who) external view returns(uint256);

/**
 * Same address as owner of the token
 */
function issuer() external view returns(address);

function allowance(address owner, address spender) external view returns(uint256);

function estimateFee(uint256 value) external view returns(uint256);

function transfer(address recipient, uint256 value) external returns(bool);

function approve(address spender, uint256 value) external returns(bool);

function transferFrom(address from, address to, uint256 value) external  returns(bool);
}


// File contracts/libraries/Address.sol
library Address {
  function isContract(address account) internal view returns(bool) {
        // This method relies on extcodesize, which returns 0 for contracts in
        // construction, since the code is only stored at the end of the
        // constructor execution.

        uint256 size;
        // solhint-disable-next-line no-inline-assembly
        assembly { size:= extcodesize(account) }
    return size > 0;
  }

  function sendValue(address payable recipient, uint256 amount) internal {
    require(address(this).balance >= amount, "Address: insufficient balance");

    // solhint-disable-next-line avoid-low-level-calls, avoid-call-value
    (bool success, ) = recipient.call{ value: amount } ("");
    require(success, "Address: unable to send value, recipient may have reverted");
  }

  function functionCall(address target, bytes memory data) internal returns(bytes memory) {
    return functionCall(target, data, "Address: low-level call failed");
  }

  function functionCall(address target, bytes memory data, string memory errorMessage) internal returns(bytes memory) {
    return functionCallWithValue(target, data, 0, errorMessage);
  }

  function functionCallWithValue(address target, bytes memory data, uint256 value) internal returns(bytes memory) {
    return functionCallWithValue(target, data, value, "Address: low-level call with value failed");
  }

  function functionCallWithValue(address target, bytes memory data, uint256 value, string memory errorMessage) internal returns(bytes memory) {
    require(address(this).balance >= value, "Address: insufficient balance for call");
    require(isContract(target), "Address: call to non-contract");

    // solhint-disable-next-line avoid-low-level-calls
    (bool success, bytes memory returndata) = target.call{ value: value } (data);
    return _verifyCallResult(success, returndata, errorMessage);
  }

  function functionStaticCall(address target, bytes memory data) internal view returns(bytes memory) {
    return functionStaticCall(target, data, "Address: low-level static call failed");
  }

  function functionStaticCall(address target, bytes memory data, string memory errorMessage) internal view returns(bytes memory) {
    require(isContract(target), "Address: static call to non-contract");

    // solhint-disable-next-line avoid-low-level-calls
    (bool success, bytes memory returndata) = target.staticcall(data);
    return _verifyCallResult(success, returndata, errorMessage);
  }

  function functionDelegateCall(address target, bytes memory data) internal returns(bytes memory) {
    return functionDelegateCall(target, data, "Address: low-level delegate call failed");
  }

  function functionDelegateCall(address target, bytes memory data, string memory errorMessage) internal returns(bytes memory) {
    require(isContract(target), "Address: delegate call to non-contract");

    // solhint-disable-next-line avoid-low-level-calls
    (bool success, bytes memory returndata) = target.delegatecall(data);
    return _verifyCallResult(success, returndata, errorMessage);
  }

  function _verifyCallResult(bool success, bytes memory returndata, string memory errorMessage) private pure returns(bytes memory) {
    if (success) {
      return returndata;
    } else {
      // Look for revert reason and bubble it up if present
      if (returndata.length > 0) {
                // The easiest way to bubble the revert reason is using memory via assembly

                // solhint-disable-next-line no-inline-assembly
                assembly {
          let returndata_size:= mload(returndata)
          revert(add(32, returndata), returndata_size)
        }
      } else {
        revert(errorMessage);
      }
    }
  }
}


// File contracts/libraries/SafeMath.sol
library SafeMath {
  /**
   * @dev Returns the addition of two unsigned integers, with an overflow flag.
   *
   * _Available since v3.4._
   */
  function tryAdd(uint256 a, uint256 b) internal pure returns(bool, uint256) {
        uint256 c = a + b;
    if (c < a) return (false, 0);
    return (true, c);
  }

  /**
   * @dev Returns the substraction of two unsigned integers, with an overflow flag.
   *
   * _Available since v3.4._
   */
  function trySub(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    if (b > a) return (false, 0);
    return (true, a - b);
  }

  /**
   * @dev Returns the multiplication of two unsigned integers, with an overflow flag.
   *
   * _Available since v3.4._
   */
  function tryMul(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    // Gas optimization: this is cheaper than requiring 'a' not being zero, but the
    // benefit is lost if 'b' is also tested.
    // See: https://github.com/OpenZeppelin/openzeppelin-contracts/pull/522
    if (a == 0) return (true, 0);
        uint256 c = a * b;
    if (c / a != b) return (false, 0);
    return (true, c);
  }

  /**
   * @dev Returns the division of two unsigned integers, with a division by zero flag.
   *
   * _Available since v3.4._
   */
  function tryDiv(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    if (b == 0) return (false, 0);
    return (true, a / b);
  }

  /**
   * @dev Returns the remainder of dividing two unsigned integers, with a division by zero flag.
   *
   * _Available since v3.4._
   */
  function tryMod(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    if (b == 0) return (false, 0);
    return (true, a % b);
  }

  function add(uint256 a, uint256 b) internal pure returns(uint256) {
        uint256 c = a + b;
    require(c >= a, "SafeMath: addition overflow");
    return c;
  }


  function sub(uint256 a, uint256 b) internal pure returns(uint256) {
    require(b <= a, "SafeMath: subtraction overflow");
    return a - b;
  }


  function mul(uint256 a, uint256 b) internal pure returns(uint256) {
    if (a == 0) return 0;
        uint256 c = a * b;
    require(c / a == b, "SafeMath: multiplication overflow");
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns(uint256) {
    require(b > 0, "SafeMath: division by zero");
    return a / b;
  }

  function mod(uint256 a, uint256 b) internal pure returns(uint256) {
    require(b > 0, "SafeMath: modulo by zero");
    return a % b;
  }


  function sub(uint256 a, uint256 b, string memory errorMessage) internal pure returns(uint256) {
    require(b <= a, errorMessage);
    return a - b;
  }

  function div(uint256 a, uint256 b, string memory errorMessage) internal pure returns(uint256) {
    require(b > 0, errorMessage);
    return a / b;
  }

  function mod(uint256 a, uint256 b, string memory errorMessage) internal pure returns(uint256) {
    require(b > 0, errorMessage);
    return a % b;
  }
}


// File contracts/VRC25.sol

/**
 * @title Base VRC25 implementation
 * @notice VRC25 implementation for opt-in to gas sponsor program. This replace Ownable from OpenZeppelin as well.
 */
abstract contract VRC25 is IVRC25, IERC165 {
  using Address for address;
    using SafeMath for uint256;

      // The order of _balances, _minFeem, _issuer must not be changed to pass validation of gas sponsor application
      mapping(address => uint256) private _balances;
    uint256 private _minFee;
    address private _owner;
    address private _newOwner;

  mapping(address => mapping(address => uint256)) private _allowances;

    string private _name;
    string private _symbol;
    uint8 private _decimals;
    uint256 private _totalSupply;

    event FeeUpdated(uint256 fee);
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

  constructor() internal {
    _name = "VETHOR FARM";
    _symbol = "VTHO FARM";
    _decimals = 18;
    _owner = msg.sender;
  }

    /**
     * @dev Throws if called by any account other than the owner.
     */
    modifier onlyOwner() {
    require(_owner == msg.sender, "VRC25: caller is not the owner");
    _;
  }

  /**
   * @notice Name of token
   */
  function name() public view returns(string memory) {
    return _name;
  }

  /**
   * @notice Symbol of token
   */
  function symbol() public view returns(string memory) {
    return _symbol;
  }

  function decimals() public view override returns(uint8) {
    return _decimals;
  }

  /**
   * @notice Returns the amount of tokens in existence.
   */
  function totalSupply() public view override returns(uint256) {
    return _totalSupply;
  }

  function balanceOf(address owner) public view override returns(uint256) {
    return _balances[owner];
  }

  function allowance(address owner, address spender) public view override returns(uint256){
    return _allowances[owner][spender];
  }

  function owner() public view returns(address) {
    return _owner;
  }

  /**
   * @notice Owner of the token
   */
  function issuer() public view override returns(address) {
    return _owner;
  }

  /**
   * @dev The amount fee that will be lost when transferring.
   */
  function minFee() public view returns(uint256) {
    return _minFee;
  }

  function estimateFee(uint256 value) public view override returns(uint256) {
    if (address(msg.sender).isContract()) {
      return 0;
    } else {
      return _estimateFee(value);
    }
  }

  function transfer(address recipient, uint256 amount) external override returns(bool) {
        uint256 fee = estimateFee(amount);
    _transfer(msg.sender, recipient, amount);
    _chargeFeeFrom(msg.sender, recipient, fee);
    return true;
  }

  function approve(address spender, uint256 amount) external override returns(bool) {
        uint256 fee = estimateFee(0);
    _approve(msg.sender, spender, amount);
    _chargeFeeFrom(msg.sender, address(this), fee);
    return true;
  }

  function transferFrom(address sender, address recipient, uint256 amount) external override returns(bool) {
        uint256 fee = estimateFee(amount);
    require(_allowances[sender][msg.sender] >= amount.add(fee), "VRC25: amount exeeds allowance");

    _allowances[sender][msg.sender] = _allowances[sender][msg.sender].sub(amount).sub(fee);
    _transfer(sender, recipient, amount);
    _chargeFeeFrom(sender, recipient, fee);
    return true;
  }

  function burn(uint256 amount) external returns(bool) {
        uint256 fee = estimateFee(0);
    _burn(msg.sender, amount);
    _chargeFeeFrom(msg.sender, address(this), fee);
    return true;
  }

  function acceptOwnership() external {
    require(msg.sender == _newOwner, "VRC25: only new owner can accept ownership");
        address oldOwner = _owner;
    _owner = _newOwner;
    _newOwner = address(0);
        emit OwnershipTransferred(oldOwner, _owner);
  }

  function transferOwnership(address newOwner) external virtual onlyOwner {
    require(newOwner != address(0), "VRC25: new owner is the zero address");
    _newOwner = newOwner;
  }

  function setFee(uint256 fee) external virtual onlyOwner {
    _minFee = fee;
        emit FeeUpdated(fee);
  }

  function supportsInterface(bytes4 interfaceId) public view override virtual returns(bool) {
    return interfaceId == type(IVRC25).interfaceId;
  }

  function _estimateFee(uint256 value) internal view virtual returns(uint256);

  /**
   * @dev Transfer token for a specified addresses
   * @param from The address to transfer from.
   * @param to The address to transfer to.
   * @param amount The amount to be transferred.
   */
  function _transfer(address from, address to, uint256 amount) internal {
    require(from != address(0), "VRC25: transfer from the zero address");
    require(to != address(0), "VRC25: transfer to the zero address");
    require(amount <= _balances[from], "VRC25: insuffient balance");
    _balances[from] = _balances[from].sub(amount);
    _balances[to] = _balances[to].add(amount);
        emit Transfer(from, to, amount);
  }

  /**
   * @dev Set allowance that spender can use from owner
   * @param owner The address that authroize the allowance
   * @param spender The address that can spend the allowance
   * @param amount The amount that can be allowed
   */
  function _approve(address owner, address spender, uint256 amount) internal {
    require(owner != address(0), "VRC25: approve from the zero address");
    require(spender != address(0), "VRC25: approve to the zero address");

    _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
  }

  /**
   * @dev Internal function to charge fee for gas sponsor function. Won't charge fee if caller is smart-contract because they are not sponsored gas.
   * NOTICE: this function is only a helper to transfer fee from an address different that msg.sender. Other validation should be handled outside of this function if necessary.
   * @param sender The address that will pay the fee
   * @param recipient The address that is destination of token transfer. If not token transfer should be address of contract
   * @param amount The amount token as fee
   */
  function _chargeFeeFrom(address sender, address recipient, uint256 amount) internal {
    if (address(msg.sender).isContract()) {
      return;
    }
    if (amount > 0) {
      _transfer(sender, _owner, amount);
            emit Fee(sender, recipient, _owner, amount);
    }
  }
  /**
   * @dev Internal function that mints an amount of the token and assigns it to
   * an account. This encapsulates the modification of balances such that the
   * proper events are emitted.
   * @param to The account that will receive the created tokens.
   * @param amount The amount that will be created.
   */
   function _mint(address to, uint256 amount) internal {
    require(to != address(0), "VRC25: mint to the zero address");
    _totalSupply = _totalSupply.add(amount);
    _balances[to] = _balances[to].add(amount);
        emit Transfer(address(0), to, amount);
  }


  /**
   * @dev Internal function that burns an amount of the token
   * This encapsulates the modification of balances such that the
   * proper events are emitted.
   * @param from The account that token amount will be deducted.
   * @param amount The amount that will be burned.
   */
  function _burn(address from, uint256 amount) internal {
    require(from != address(0), "VRC25: burn from the zero address");
    require(amount <= _balances[from], "VRC25: insuffient balance");
    _totalSupply = _totalSupply.sub(amount);
    _balances[from] = _balances[from].sub(amount);
        emit Transfer(from, address(0), amount);
  }
}


// File contracts/interfaces/IVRC25Permit.sol

/**
 * @dev Interface of the ERC20 Permit extension allowing approvals to be made via signatures, as defined in
 * https://eips.ethereum.org/EIPS/eip-2612[EIP-2612].
 *
 * Adds the {permit} method, which can be used to change an account's ERC20 allowance (see {IERC20-allowance}) by
 * presenting a message signed by the account. By not relying on {IERC20-approve}, the token holder account doesn't
 * need to send a transaction, and thus is not required to hold Ether at all.
 */
interface IVRC25Permit {
    /**
     * @dev Returns the domain separator used in the encoding of the signature for {permit}, as defined by {EIP712}.
     */
    // solhint-disable-next-line func-name-mixedcase
    function DOMAIN_SEPARATOR() external view returns(bytes32);


function nonces(address owner) external view returns(uint256);
}


// File contracts/libraries/ECDSA.sol

/**
 * @dev Elliptic Curve Digital Signature Algorithm (ECDSA) operations.
 *
 * These functions can be used to verify that a message was signed by the holder
 * of the private keys of a given address.
 */
library ECDSA {
  function recover(bytes32 hash, bytes memory signature) internal pure returns(address) {
    // Check the signature length
    if (signature.length != 65) {
      revert("ECDSA: invalid signature length");
    }

        // Divide the signature in r, s and v variables
        bytes32 r;
        bytes32 s;
        uint8 v;

        // ecrecover takes the signature parameters, and the only way to get them
        // currently is to use assembly.
        // solhint-disable-next-line no-inline-assembly
        assembly {
      r:= mload(add(signature, 0x20))
      s:= mload(add(signature, 0x40))
      v:= byte(0, mload(add(signature, 0x60)))
    }

    return recover(hash, v, r, s);
  }

  function recover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) internal pure returns(address) {
    // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
    // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
    // the valid range for s in (281): 0 < s < secp256k1n ÷ 2 + 1, and for v in (282): v ∈ {27, 28}. Most
    // signatures from current libraries generate a unique signature with an s-value in the lower half order.
    //
    // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
    // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
    // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
    // these malleable signatures as well.
    require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "ECDSA: invalid signature 's' value");
    require(v == 27 || v == 28, "ECDSA: invalid signature 'v' value");

        // If the signature is valid (and not malleable), return the signer address
        address signer = ecrecover(hash, v, r, s);
    require(signer != address(0), "ECDSA: invalid signature");

    return signer;
  }
}


// File contracts/libraries/EIP712.sol

abstract contract EIP712 {
    /* solhint-disable var-name-mixedcase */
    // Cache the domain separator as an immutable value, but also store the chain id that it corresponds to, in order to
    // invalidate the cached domain separator if the chain id changes.
    bytes32 private immutable _CACHED_DOMAIN_SEPARATOR;
    uint256 private immutable _CACHED_CHAIN_ID;
    address private immutable _CACHED_THIS;

    bytes32 private immutable _HASHED_NAME;
    bytes32 private immutable _HASHED_VERSION;
    bytes32 private immutable _TYPE_HASH;

  /* solhint-enable var-name-mixedcase */

  constructor(string memory name, string memory version) internal {
        bytes32 hashedName = keccak256(bytes(name));
        bytes32 hashedVersion = keccak256(bytes(version));
        bytes32 typeHash = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
    _HASHED_NAME = hashedName;
    _HASHED_VERSION = hashedVersion;
    _CACHED_CHAIN_ID = _getChainId();
    _CACHED_DOMAIN_SEPARATOR = _buildDomainSeparator(typeHash, hashedName, hashedVersion);
    _CACHED_THIS = address(this);
    _TYPE_HASH = typeHash;
  }

  /**
   * @dev Returns the domain separator for the current chain.
   */
  function _domainSeparatorV4() internal view returns(bytes32) {
    if (address(this) == _CACHED_THIS && _getChainId() == _CACHED_CHAIN_ID) {
      return _CACHED_DOMAIN_SEPARATOR;
    } else {
      return _buildDomainSeparator(_TYPE_HASH, _HASHED_NAME, _HASHED_VERSION);
    }
  }

  function _buildDomainSeparator(bytes32 typeHash, bytes32 nameHash, bytes32 versionHash) private view returns(bytes32) {
    return keccak256(abi.encode(typeHash, nameHash, versionHash, _getChainId(), address(this)));
  }

  function _getChainId() private view returns(uint256 chainId) {
    this; // silence state mutability warning without generating bytecode - see https://github.com/ethereum/solidity/issues/2691
        // solhint-disable-next-line no-inline-assembly
        assembly {
      chainId:= chainid()
    }
  }
}


// File contracts/VRC25Permit.sol

abstract contract VRC25Permit is VRC25, EIP712, IVRC25Permit {
    bytes32 private constant PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");

  mapping(address => uint256) private _nonces;

  constructor() EIP712("VRC25", "1") { }

  /**
   * @dev See {IERC20Permit-DOMAIN_SEPARATOR}.
   */
  // solhint-disable-next-line func-name-mixedcase
  function DOMAIN_SEPARATOR() external override view returns(bytes32) {
    return _domainSeparatorV4();
  }

  /**
   * @dev Returns an the next unused nonce for an address.
   */
  function nonces(address owner) public view virtual override(IVRC25Permit) returns(uint256) {
    return _nonces[owner];
  }

  /**
   * @dev Consumes a nonce.
   *
   * Returns the current value and increments nonce.
   */
  function _useNonce(address owner) internal returns(uint256) {
    // For each account, the nonce has an initial value of 0, can only be incremented by one, and cannot be
    // decremented or reset. This guarantees that the nonce never overflows.
    // It is important to do x++ and not ++x here.
    return _nonces[owner]++;
  }
}

// File: contracts/MyVRC25Mintable.sol


pragma solidity >= 0.6.2;


contract VETHORFARM is VRC25Permit {
  using Address for address;
  

  constructor() VRC25() {
    uint256 fractions = 10 ** uint256(18);
    _mint(msg.sender, 42000000 * fractions);
    
  }

  /**
   * @notice Calculate fee required for action related to this token
   * @param value Amount of fee
   */
  function _estimateFee(uint256 value) internal view override returns(uint256) {
    return value + minFee();
  }

  function supportsInterface(bytes4 interfaceId) public view override returns(bool) {
    return interfaceId == type(IVRC25).interfaceId || super.supportsInterface(interfaceId);
  }

  function mint(address recipient, uint256 amount) external onlyOwner returns(bool) {
    
    _mint(recipient, amount);
    return true;
  }
}
// File: contracts/VRC25Permit.sol

pragma solidity >= 0.6.2;

// Sources flattened with hardhat v2.12.6 https://hardhat.org

// File contracts/interfaces/IERC165.sol

/**
 * @dev Interface of the ERC165 standard, as defined in the
 * https://eips.ethereum.org/EIPS/eip-165[EIP].
 *
 * Implementers can declare support of contract interfaces, which can then be
 * queried by others ({ERC165Checker}).
 *
 * For an implementation, see {ERC165}.
 */
interface IERC165 {
    function supportsInterface(bytes4 interfaceId) external view returns(bool);
}


// File contracts/interfaces/IVRC25.sol

/**
 * @dev Interface of the VRC25 standard as defined in the EIP.
 */
interface IVRC25 {
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event Fee(address indexed from, address indexed to, address indexed issuer, uint256 value);

function decimals() external view returns(uint8);

/**
 * @notice Returns the amount of tokens in existence.
 */
function totalSupply() external view returns(uint256);

function balanceOf(address who) external view returns(uint256);

/**
 * Same address as owner of the token
 */
function issuer() external view returns(address);

function allowance(address owner, address spender) external view returns(uint256);

function estimateFee(uint256 value) external view returns(uint256);

function transfer(address recipient, uint256 value) external returns(bool);

function approve(address spender, uint256 value) external returns(bool);

function transferFrom(address from, address to, uint256 value) external  returns(bool);
}


// File contracts/libraries/Address.sol
library Address {
  function isContract(address account) internal view returns(bool) {
        // This method relies on extcodesize, which returns 0 for contracts in
        // construction, since the code is only stored at the end of the
        // constructor execution.

        uint256 size;
        // solhint-disable-next-line no-inline-assembly
        assembly { size:= extcodesize(account) }
    return size > 0;
  }

  function sendValue(address payable recipient, uint256 amount) internal {
    require(address(this).balance >= amount, "Address: insufficient balance");

    // solhint-disable-next-line avoid-low-level-calls, avoid-call-value
    (bool success, ) = recipient.call{ value: amount } ("");
    require(success, "Address: unable to send value, recipient may have reverted");
  }

  function functionCall(address target, bytes memory data) internal returns(bytes memory) {
    return functionCall(target, data, "Address: low-level call failed");
  }

  function functionCall(address target, bytes memory data, string memory errorMessage) internal returns(bytes memory) {
    return functionCallWithValue(target, data, 0, errorMessage);
  }

  function functionCallWithValue(address target, bytes memory data, uint256 value) internal returns(bytes memory) {
    return functionCallWithValue(target, data, value, "Address: low-level call with value failed");
  }

  function functionCallWithValue(address target, bytes memory data, uint256 value, string memory errorMessage) internal returns(bytes memory) {
    require(address(this).balance >= value, "Address: insufficient balance for call");
    require(isContract(target), "Address: call to non-contract");

    // solhint-disable-next-line avoid-low-level-calls
    (bool success, bytes memory returndata) = target.call{ value: value } (data);
    return _verifyCallResult(success, returndata, errorMessage);
  }

  function functionStaticCall(address target, bytes memory data) internal view returns(bytes memory) {
    return functionStaticCall(target, data, "Address: low-level static call failed");
  }

  function functionStaticCall(address target, bytes memory data, string memory errorMessage) internal view returns(bytes memory) {
    require(isContract(target), "Address: static call to non-contract");

    // solhint-disable-next-line avoid-low-level-calls
    (bool success, bytes memory returndata) = target.staticcall(data);
    return _verifyCallResult(success, returndata, errorMessage);
  }

  function functionDelegateCall(address target, bytes memory data) internal returns(bytes memory) {
    return functionDelegateCall(target, data, "Address: low-level delegate call failed");
  }

  function functionDelegateCall(address target, bytes memory data, string memory errorMessage) internal returns(bytes memory) {
    require(isContract(target), "Address: delegate call to non-contract");

    // solhint-disable-next-line avoid-low-level-calls
    (bool success, bytes memory returndata) = target.delegatecall(data);
    return _verifyCallResult(success, returndata, errorMessage);
  }

  function _verifyCallResult(bool success, bytes memory returndata, string memory errorMessage) private pure returns(bytes memory) {
    if (success) {
      return returndata;
    } else {
      // Look for revert reason and bubble it up if present
      if (returndata.length > 0) {
                // The easiest way to bubble the revert reason is using memory via assembly

                // solhint-disable-next-line no-inline-assembly
                assembly {
          let returndata_size:= mload(returndata)
          revert(add(32, returndata), returndata_size)
        }
      } else {
        revert(errorMessage);
      }
    }
  }
}


// File contracts/libraries/SafeMath.sol
library SafeMath {
  /**
   * @dev Returns the addition of two unsigned integers, with an overflow flag.
   *
   * _Available since v3.4._
   */
  function tryAdd(uint256 a, uint256 b) internal pure returns(bool, uint256) {
        uint256 c = a + b;
    if (c < a) return (false, 0);
    return (true, c);
  }

  /**
   * @dev Returns the substraction of two unsigned integers, with an overflow flag.
   *
   * _Available since v3.4._
   */
  function trySub(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    if (b > a) return (false, 0);
    return (true, a - b);
  }

  /**
   * @dev Returns the multiplication of two unsigned integers, with an overflow flag.
   *
   * _Available since v3.4._
   */
  function tryMul(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    // Gas optimization: this is cheaper than requiring 'a' not being zero, but the
    // benefit is lost if 'b' is also tested.
    // See: https://github.com/OpenZeppelin/openzeppelin-contracts/pull/522
    if (a == 0) return (true, 0);
        uint256 c = a * b;
    if (c / a != b) return (false, 0);
    return (true, c);
  }

  /**
   * @dev Returns the division of two unsigned integers, with a division by zero flag.
   *
   * _Available since v3.4._
   */
  function tryDiv(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    if (b == 0) return (false, 0);
    return (true, a / b);
  }

  /**
   * @dev Returns the remainder of dividing two unsigned integers, with a division by zero flag.
   *
   * _Available since v3.4._
   */
  function tryMod(uint256 a, uint256 b) internal pure returns(bool, uint256) {
    if (b == 0) return (false, 0);
    return (true, a % b);
  }

  function add(uint256 a, uint256 b) internal pure returns(uint256) {
        uint256 c = a + b;
    require(c >= a, "SafeMath: addition overflow");
    return c;
  }


  function sub(uint256 a, uint256 b) internal pure returns(uint256) {
    require(b <= a, "SafeMath: subtraction overflow");
    return a - b;
  }


  function mul(uint256 a, uint256 b) internal pure returns(uint256) {
    if (a == 0) return 0;
        uint256 c = a * b;
    require(c / a == b, "SafeMath: multiplication overflow");
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns(uint256) {
    require(b > 0, "SafeMath: division by zero");
    return a / b;
  }

  function mod(uint256 a, uint256 b) internal pure returns(uint256) {
    require(b > 0, "SafeMath: modulo by zero");
    return a % b;
  }


  function sub(uint256 a, uint256 b, string memory errorMessage) internal pure returns(uint256) {
    require(b <= a, errorMessage);
    return a - b;
  }

  function div(uint256 a, uint256 b, string memory errorMessage) internal pure returns(uint256) {
    require(b > 0, errorMessage);
    return a / b;
  }

  function mod(uint256 a, uint256 b, string memory errorMessage) internal pure returns(uint256) {
    require(b > 0, errorMessage);
    return a % b;
  }
}


// File contracts/VRC25.sol

/**
 * @title Base VRC25 implementation
 * @notice VRC25 implementation for opt-in to gas sponsor program. This replace Ownable from OpenZeppelin as well.
 */
abstract contract VRC25 is IVRC25, IERC165 {
  using Address for address;
    using SafeMath for uint256;

      // The order of _balances, _minFeem, _issuer must not be changed to pass validation of gas sponsor application
      mapping(address => uint256) private _balances;
    uint256 private _minFee;
    address private _owner;
    address private _newOwner;

  mapping(address => mapping(address => uint256)) private _allowances;

    string private _name;
    string private _symbol;
    uint8 private _decimals;
    uint256 private _totalSupply;

    event FeeUpdated(uint256 fee);
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

  constructor() internal {
    _name = "VETHOR FARM";
    _symbol = "VTHO FARM";
    _decimals = 18;
    _owner = msg.sender;
  }

    /**
     * @dev Throws if called by any account other than the owner.
     */
    modifier onlyOwner() {
    require(_owner == msg.sender, "VRC25: caller is not the owner");
    _;
  }

  /**
   * @notice Name of token
   */
  function name() public view returns(string memory) {
    return _name;
  }

  /**
   * @notice Symbol of token
   */
  function symbol() public view returns(string memory) {
    return _symbol;
  }

  function decimals() public view override returns(uint8) {
    return _decimals;
  }

  /**
   * @notice Returns the amount of tokens in existence.
   */
  function totalSupply() public view override returns(uint256) {
    return _totalSupply;
  }

  function balanceOf(address owner) public view override returns(uint256) {
    return _balances[owner];
  }

  function allowance(address owner, address spender) public view override returns(uint256){
    return _allowances[owner][spender];
  }

  function owner() public view returns(address) {
    return _owner;
  }

  /**
   * @notice Owner of the token
   */
  function issuer() public view override returns(address) {
    return _owner;
  }

  /**
   * @dev The amount fee that will be lost when transferring.
   */
  function minFee() public view returns(uint256) {
    return _minFee;
  }

  function estimateFee(uint256 value) public view override returns(uint256) {
    if (address(msg.sender).isContract()) {
      return 0;
    } else {
      return _estimateFee(value);
    }
  }

  function transfer(address recipient, uint256 amount) external override returns(bool) {
        uint256 fee = estimateFee(amount);
    _transfer(msg.sender, recipient, amount);
    _chargeFeeFrom(msg.sender, recipient, fee);
    return true;
  }

  function approve(address spender, uint256 amount) external override returns(bool) {
        uint256 fee = estimateFee(0);
    _approve(msg.sender, spender, amount);
    _chargeFeeFrom(msg.sender, address(this), fee);
    return true;
  }

  function transferFrom(address sender, address recipient, uint256 amount) external override returns(bool) {
        uint256 fee = estimateFee(amount);
    require(_allowances[sender][msg.sender] >= amount.add(fee), "VRC25: amount exeeds allowance");

    _allowances[sender][msg.sender] = _allowances[sender][msg.sender].sub(amount).sub(fee);
    _transfer(sender, recipient, amount);
    _chargeFeeFrom(sender, recipient, fee);
    return true;
  }

  function burn(uint256 amount) external returns(bool) {
        uint256 fee = estimateFee(0);
    _burn(msg.sender, amount);
    _chargeFeeFrom(msg.sender, address(this), fee);
    return true;
  }

  function acceptOwnership() external {
    require(msg.sender == _newOwner, "VRC25: only new owner can accept ownership");
        address oldOwner = _owner;
    _owner = _newOwner;
    _newOwner = address(0);
        emit OwnershipTransferred(oldOwner, _owner);
  }

  function transferOwnership(address newOwner) external virtual onlyOwner {
    require(newOwner != address(0), "VRC25: new owner is the zero address");
    _newOwner = newOwner;
  }

  function setFee(uint256 fee) external virtual onlyOwner {
    _minFee = fee;
        emit FeeUpdated(fee);
  }

  function supportsInterface(bytes4 interfaceId) public view override virtual returns(bool) {
    return interfaceId == type(IVRC25).interfaceId;
  }

  function _estimateFee(uint256 value) internal view virtual returns(uint256);

  /**
   * @dev Transfer token for a specified addresses
   * @param from The address to transfer from.
   * @param to The address to transfer to.
   * @param amount The amount to be transferred.
   */
  function _transfer(address from, address to, uint256 amount) internal {
    require(from != address(0), "VRC25: transfer from the zero address");
    require(to != address(0), "VRC25: transfer to the zero address");
    require(amount <= _balances[from], "VRC25: insuffient balance");
    _balances[from] = _balances[from].sub(amount);
    _balances[to] = _balances[to].add(amount);
        emit Transfer(from, to, amount);
  }

  /**
   * @dev Set allowance that spender can use from owner
   * @param owner The address that authroize the allowance
   * @param spender The address that can spend the allowance
   * @param amount The amount that can be allowed
   */
  function _approve(address owner, address spender, uint256 amount) internal {
    require(owner != address(0), "VRC25: approve from the zero address");
    require(spender != address(0), "VRC25: approve to the zero address");

    _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
  }

  /**
   * @dev Internal function to charge fee for gas sponsor function. Won't charge fee if caller is smart-contract because they are not sponsored gas.
   * NOTICE: this function is only a helper to transfer fee from an address different that msg.sender. Other validation should be handled outside of this function if necessary.
   * @param sender The address that will pay the fee
   * @param recipient The address that is destination of token transfer. If not token transfer should be address of contract
   * @param amount The amount token as fee
   */
  function _chargeFeeFrom(address sender, address recipient, uint256 amount) internal {
    if (address(msg.sender).isContract()) {
      return;
    }
    if (amount > 0) {
      _transfer(sender, _owner, amount);
            emit Fee(sender, recipient, _owner, amount);
    }
  }
  /**
   * @dev Internal function that mints an amount of the token and assigns it to
   * an account. This encapsulates the modification of balances such that the
   * proper events are emitted.
   * @param to The account that will receive the created tokens.
   * @param amount The amount that will be created.
   */
   function _mint(address to, uint256 amount) internal {
    require(to != address(0), "VRC25: mint to the zero address");
    _totalSupply = _totalSupply.add(amount);
    _balances[to] = _balances[to].add(amount);
        emit Transfer(address(0), to, amount);
  }


  /**
   * @dev Internal function that burns an amount of the token
   * This encapsulates the modification of balances such that the
   * proper events are emitted.
   * @param from The account that token amount will be deducted.
   * @param amount The amount that will be burned.
   */
  function _burn(address from, uint256 amount) internal {
    require(from != address(0), "VRC25: burn from the zero address");
    require(amount <= _balances[from], "VRC25: insuffient balance");
    _totalSupply = _totalSupply.sub(amount);
    _balances[from] = _balances[from].sub(amount);
        emit Transfer(from, address(0), amount);
  }
}


// File contracts/interfaces/IVRC25Permit.sol

/**
 * @dev Interface of the ERC20 Permit extension allowing approvals to be made via signatures, as defined in
 * https://eips.ethereum.org/EIPS/eip-2612[EIP-2612].
 *
 * Adds the {permit} method, which can be used to change an account's ERC20 allowance (see {IERC20-allowance}) by
 * presenting a message signed by the account. By not relying on {IERC20-approve}, the token holder account doesn't
 * need to send a transaction, and thus is not required to hold Ether at all.
 */
interface IVRC25Permit {
    /**
     * @dev Returns the domain separator used in the encoding of the signature for {permit}, as defined by {EIP712}.
     */
    // solhint-disable-next-line func-name-mixedcase
    function DOMAIN_SEPARATOR() external view returns(bytes32);


function nonces(address owner) external view returns(uint256);
}


// File contracts/libraries/ECDSA.sol

/**
 * @dev Elliptic Curve Digital Signature Algorithm (ECDSA) operations.
 *
 * These functions can be used to verify that a message was signed by the holder
 * of the private keys of a given address.
 */
library ECDSA {
  function recover(bytes32 hash, bytes memory signature) internal pure returns(address) {
    // Check the signature length
    if (signature.length != 65) {
      revert("ECDSA: invalid signature length");
    }

        // Divide the signature in r, s and v variables
        bytes32 r;
        bytes32 s;
        uint8 v;

        // ecrecover takes the signature parameters, and the only way to get them
        // currently is to use assembly.
        // solhint-disable-next-line no-inline-assembly
        assembly {
      r:= mload(add(signature, 0x20))
      s:= mload(add(signature, 0x40))
      v:= byte(0, mload(add(signature, 0x60)))
    }

    return recover(hash, v, r, s);
  }

  function recover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) internal pure returns(address) {
    // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
    // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
    // the valid range for s in (281): 0 < s < secp256k1n ÷ 2 + 1, and for v in (282): v ∈ {27, 28}. Most
    // signatures from current libraries generate a unique signature with an s-value in the lower half order.
    //
    // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
    // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
    // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
    // these malleable signatures as well.
    require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "ECDSA: invalid signature 's' value");
    require(v == 27 || v == 28, "ECDSA: invalid signature 'v' value");

        // If the signature is valid (and not malleable), return the signer address
        address signer = ecrecover(hash, v, r, s);
    require(signer != address(0), "ECDSA: invalid signature");

    return signer;
  }
}


// File contracts/libraries/EIP712.sol

abstract contract EIP712 {
    /* solhint-disable var-name-mixedcase */
    // Cache the domain separator as an immutable value, but also store the chain id that it corresponds to, in order to
    // invalidate the cached domain separator if the chain id changes.
    bytes32 private immutable _CACHED_DOMAIN_SEPARATOR;
    uint256 private immutable _CACHED_CHAIN_ID;
    address private immutable _CACHED_THIS;

    bytes32 private immutable _HASHED_NAME;
    bytes32 private immutable _HASHED_VERSION;
    bytes32 private immutable _TYPE_HASH;

  /* solhint-enable var-name-mixedcase */

  constructor(string memory name, string memory version) internal {
        bytes32 hashedName = keccak256(bytes(name));
        bytes32 hashedVersion = keccak256(bytes(version));
        bytes32 typeHash = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
    _HASHED_NAME = hashedName;
    _HASHED_VERSION = hashedVersion;
    _CACHED_CHAIN_ID = _getChainId();
    _CACHED_DOMAIN_SEPARATOR = _buildDomainSeparator(typeHash, hashedName, hashedVersion);
    _CACHED_THIS = address(this);
    _TYPE_HASH = typeHash;
  }

  /**
   * @dev Returns the domain separator for the current chain.
   */
  function _domainSeparatorV4() internal view returns(bytes32) {
    if (address(this) == _CACHED_THIS && _getChainId() == _CACHED_CHAIN_ID) {
      return _CACHED_DOMAIN_SEPARATOR;
    } else {
      return _buildDomainSeparator(_TYPE_HASH, _HASHED_NAME, _HASHED_VERSION);
    }
  }

  function _buildDomainSeparator(bytes32 typeHash, bytes32 nameHash, bytes32 versionHash) private view returns(bytes32) {
    return keccak256(abi.encode(typeHash, nameHash, versionHash, _getChainId(), address(this)));
  }

  function _getChainId() private view returns(uint256 chainId) {
    this; // silence state mutability warning without generating bytecode - see https://github.com/ethereum/solidity/issues/2691
        // solhint-disable-next-line no-inline-assembly
        assembly {
      chainId:= chainid()
    }
  }
}


// File contracts/VRC25Permit.sol

abstract contract VRC25Permit is VRC25, EIP712, IVRC25Permit {
    bytes32 private constant PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");

  mapping(address => uint256) private _nonces;

  constructor() EIP712("VRC25", "1") { }

  /**
   * @dev See {IERC20Permit-DOMAIN_SEPARATOR}.
   */
  // solhint-disable-next-line func-name-mixedcase
  function DOMAIN_SEPARATOR() external override view returns(bytes32) {
    return _domainSeparatorV4();
  }

  /**
   * @dev Returns an the next unused nonce for an address.
   */
  function nonces(address owner) public view virtual override(IVRC25Permit) returns(uint256) {
    return _nonces[owner];
  }

  /**
   * @dev Consumes a nonce.
   *
   * Returns the current value and increments nonce.
   */
  function _useNonce(address owner) internal returns(uint256) {
    // For each account, the nonce has an initial value of 0, can only be incremented by one, and cannot be
    // decremented or reset. This guarantees that the nonce never overflows.
    // It is important to do x++ and not ++x here.
    return _nonces[owner]++;
  }
}

// File: contracts/MyVRC25Mintable.sol


pragma solidity >= 0.6.2;


contract VETHORFARM is VRC25Permit {
  using Address for address;
  

  constructor() VRC25() {
    uint256 fractions = 10 ** uint256(18);
    _mint(msg.sender, 42000000 * fractions);
    
  }

  /**
   * @notice Calculate fee required for action related to this token
   * @param value Amount of fee
   */
  function _estimateFee(uint256 value) internal view override returns(uint256) {
    return value + minFee();
  }

  function supportsInterface(bytes4 interfaceId) public view override returns(bool) {
    return interfaceId == type(IVRC25).interfaceId || super.supportsInterface(interfaceId);
  }

  function mint(address recipient, uint256 amount) external onlyOwner returns(bool) {
    
    _mint(recipient, amount);
    return true;
  }
}

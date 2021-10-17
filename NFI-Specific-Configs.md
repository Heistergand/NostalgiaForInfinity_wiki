# Hold Trades

 -------- SPECIFIC TRADES 
   
   In case you want to have SOME of the trades to only be sold when on profit, add a file named        
   "nfi-hold-trades.json" in the user_data directory                                                   
                                                                                                       
   The contents should be similar to:                                                                  
                                                                                                       
   {"trade_ids": [1, 3, 7], "profit_ratio": 0.005}                                                     
                                                                                                       
   Or, for individual profit ratios(Notice the trade ID's as strings:                                  
                                                                                                       
   {"trade_ids": {"1": 0.001, "3": -0.005, "7": 0.05}}                                                 
                                                                                                       
   NOTE:                                                                                               
    * `trade_ids` is a list of integers, the trade ID's, which you can get from the logs or from the   
      output of the telegram status command.                                                           
    * Regardless of the defined profit ratio(s), the strategy MUST still produce a SELL signal for the 
      HOLD support logic to run                                                                        
    * This feature can be completely disabled with the holdSupportEnabled class attribute              
                                                                                                       
 -------- SPECIFIC PAIRS 
   
   In case you want to have some pairs to always be on held until a specific profit, using the same    
   "hold-trades.json" file add something like:                                                         
                                                                                                       
   {"trade_pairs": {"BTC/USDT": 0.001, "ETH/USDT": -0.005}}                                            
                                                                                                       
 -------- SPECIFIC TRADES AND PAIRS 
   It is also valid to include specific trades and pairs on the holds file, for example:               
                                                                                                       
   {"trade_ids": {"1": 0.001}, "trade_pairs": {"BTC/USDT": 0.001}}

# Splitting Config files

This is not per se NFI specific , but just some good practice and a nifty thing to do , You can split sections of your configs into separate config files in order to neatly organize them or to separate confidential information from the actual config (makes sharing the configs easier)

[here is the link for the docs](https://www.freqtrade.io/en/stable/configuration/#multiple-configuration-files)


# Some Fine Tunable Stuff

 -------- Pairlists and Blacklists
So for Kucoin and Binance (BUSD and USDT) , there are recommended volume pairlist configurations and blacklists available that u can either incorporate to ur config files or use some git/symlink magic and directly use the ones from the repo and update them easily . [Here are the configs](https://github.com/iterativv/NostalgiaForInfinity/tree/main/configs)


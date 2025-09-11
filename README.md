# Hardhat-version-3-Guide
A guide for deployment, test, and verification of hardhat version 3.

## After installation of the Project using Hardhat version 3 
- install hardhat Keystore dependency to access keystore when deploying
```
    npx hardhat -D @nomicfoundation/hardhat-keystore
```
or using pnpm
```
    pnpm add -D @nomicfoundation/hardhat-keystore
```

- install the hardhat verify dependency

```
    npx hardhat -D @nomicfoundation/hardhat-verify";
```
or using pnpm
```
    pnpm add -D @nomicfoundation/hardhat-keystore
```

- add Your environment variables using keystore instead of using the custom VARS in V2
```
    hardhat keystore set ENVIRONMENT_VARIABLE
```
after setting your Environment variables, your hardhat config.ts should look like this
```
    import type { HardhatUserConfig } from "hardhat/config";
    import hardhatVerify from "@nomicfoundation/hardhat-verify";
    import hardhatKeystore from "@nomicfoundation/hardhat-Keystore";
    import hardhatToolboxMochaEthersPlugin from "@nomicfoundation/hardhat-toolbox-mocha-ethers";
    import { configVariable } from "hardhat/config";

    const config: HardhatUserConfig = {
    plugins: [hardhatToolboxMochaEthersPlugin, hardhatVerify, hardhatKeystore],
    solidity: {
        profiles: {
        default: {
            version: "0.8.28",
        },
        production: {
            version: "0.8.28",
            settings: {
            optimizer: {
                enabled: true,
                runs: 200,
            },
            viaIR: true,
            },
        },
        },
    },
    networks: {
        liskSepolia: {
            type: "http",
            url:configVariable("LISK_SEPOLIA_URL"),
            accounts: [configVariable("PRIVATE_KEY")],
            chainId: 4202,
        },
        hardhatMainnet: {
            type: "edr-simulated",
            chainType: "l1",
        },
        hardhatOp: {
            type: "edr-simulated",
            chainType: "op",
        },
    },
    verify: {
        etherscan: {
            apiKey: configVariable("ETHERSCAN_API_KEY"),
        },
        blockscout: {
            enabled: true,
        }
    },
        chainDescriptors: {
        4202: {
        name: "Listk Sepolia Blockscout",
        blockExplorers: {
            blockscout: {
                name: "Lisk Sepolia Blockscout",
                url: "https://rpc.sepolia-api.lisk.com",
                apiUrl: "https://sepolia-blockscout.lisk.com/api",
            },
            etherscan: {
                name: "Lisk Sepolia Blockscout",
                url: "https://rpc.sepolia-api.lisk.com",
                apiUrl: "https://sepolia-blockscout.lisk.com/api",
            },
        },
        },
    },
    };

    export default config;
```

- to deploy your contract

```
    npx hardhat ignition deploy --network <THE NETWORK YOU USED IN YOUR CONFIG i.e liskSepolia in this case> ignition/modules/Counter.ts 
```

or with pnpm 

```
    pnpm hardhat ignition deploy --network liskSepolia ignition/modules/FILENAME.ts 
```

- After the deploment create a file to programattically verify your deployed contract
- create your verification script file.
 After creating your verification script file, its content should look like this

```
    import hre from "hardhat";
    import { verifyContract } from "@nomicfoundation/hardhat-verify/verify";

    await verifyContract(
    {
        address: "YOUR DEPLOYED CONTRACT ADDRESS",
        constructorArgs: [YOUR CONTRACT ARGS], // you can comment this line is your contract has no arguement
        provider: "etherscan",
    },
    hre,
    );
```

- build your project again using 

```
    npx hardhat build --build-profile production
``` 
    or using pnpm 
```
    pnpm dlx hardhat build --build-profile production
```
- Run the script using 
```
    hardhat verify --network <YOUR NETWORK TO DEPLOY AS IT IS IN THE HARDHAT CONFIG (liskSepolia in this case)> <DEPLOYED CONTRACT ADDRESS> 
```
or 
```
    pnpm dlx hardhat verify --network <YOUR NETWORK TO DEPLOY AS IT IS IN THE HARDHAT CONFIG (liskSepolia in this case)> <DEPLOYED CONTRACT ADDRESS> 
```

### Congrats 🎉🎉
you have successfully verified your contract 
you can check the network explorer

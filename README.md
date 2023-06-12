# InverterProtocolArchitecture
The document for understanding Inverter Protocol's Technical Specifications
Introduction 
The absence of modular and flexible building blocks for resource flow coordination protocols between multiple parties creates significant challenges for builders of decentralized coordination systems. Builders must struggle with complex interactions and relationships that abstract away these complexities from users without falling back to centralization tendencies. Nevertheless, these inflexible systems cannot accommodate an ever-evolving complexity and adaptability to future use cases. Such inefficiencies, elevated risks, and hindered innovation ultimately limit the potential impact and widespread adoption of decentralized technologies that could revolutionize industries and scale coordination.
Inverter Protocol is a decentralized coordination protocol that enables programmable asset flows between parties. The protocol is designed to support any project or use case that requires the exchange of resources between multiple parties while ensuring openness, adaptability, and ease of use. At its core, the Inverter Protocol consists of a modular architecture that seamlessly integrates different modules and existing protocols. This modular approach enables developers to create new modules that can be added to the protocol, allowing for an ever-expanding range of use cases and applications. 
Inverter Protocol is built on top of Ethereum and leverages the latest standards and best practices in smart contract development. The protocol uses EIP-1167-based proxies for deploying new contracts, ensuring a gas-efficient and cost-effective solution. One of the key benefits of Inverter Protocol is its focus on security. The protocol comprises audited and community-accepted modules that have been tested, audited, and proven to work.
While Inverter was initially conceived as a dynamic funding protocol for projects and contributors with multiple funders, it has evolved to become much more than that. It is now a programmable resource flow that can be used in a wide variety of use cases, from conditioned payment streams over dynamic staking mechanisms to fully-fledged protocols 

Requirements

Inverter Protocol is designed to provide a flexible and extensible way for any project or protocol to exchange assets between parties programmatically. As such, it must meet certain requirements to ensure its effectiveness and usefulness for the wider open-source and blockchain communities. To create a technical specification of the smart contracts, we must clearly define these requirements. The main goal of the technical specification is to ensure that these high-level requirements are met, even as the implementation evolves beyond the MVP phase. By doing so, we can create a foundation that can be extended and adapted to meet the needs of different projects and use cases without requiring a complete rewrite of the underlying code.

## High-Level Requirements

High-level requirements are overarching principles and goals that guide the development of a system or project. They provide a clear understanding of what the system aims to achieve, its intended functionality, and the constraints it must operate within. In the context of Inverter, high-level requirements ensure that the protocol meets certain standards of security, compatibility, and usability, allowing it to be effectively integrated into a wider range of applications. These requirements are:
**● Modular Architecture**
○ The overall design strives for modularity by distinctly separating the various
steps, protocols, and actors from the code's foundation. The overall functionality
is divided between a core orchestrator contract and several modules that can be
activated or deactivated based on the specific requirements of the use case.
○ There is a high-level interface for each module that the orchestrator contract can rely on when communicating with them. On top of that, each module should have a clear and concise interface that outlines the expected input parameters and
output data, ensuring that each module can be easily understood and tested.
○ Modules follow a standardized format such that external contributors may easily
create their own modules without spending too much time understanding the
intricacies of our protocol.
○ Using a modular architecture allows for easier code base maintenance, enabling
developers to fix bugs and update features without overhauling the entire system.
**● Governed Module-Library**
○ The Inverter Protocol has a module library that lists all the available modules that the contracts can use. A governance mechanism will maintain the library, allowing users to add, remove or update modules based on.
○ This process may happen through a combination of on-chain and off-chain governance mechanisms to ensure a balance between decentralization and efficiency.
○ The module library is designed to be easily integrated with other libraries like Safe. This gives developers and users more choices for building and interacting and allows us to leverage proven contracts within the ecosystem. Compatibility and interoperability are key priorities.
○ The curation of the module library prioritizes security and diligence considerations, as the integrity and safety of the system depend on a safe and well-maintained library. Any changes or additions to the module library are undergoing external audits from multiple auditors.
○ In addition to auditing, we have implemented fallback mechanisms to handle potential issues such as locked funds. Users can still withdraw their deposits and funds in such scenarios to avoid negative impacts.

### Low-Level Requirements

  

Low-level requirements of the Inverter Protocol smart contracts are  specific functionalities the code must fulfill:

-Each system is created on its own smart contract instance, keeping the funds separate from all other protocol users.
-When creating a new orchestrator contract, users choose from a set of modules to activate.
-   When making use of the native modules, e.g., a default governance or funding manager module, the interfaces and “separation of concerns” defined by the protocol need to ensure that all additional modules can interact with each other and benefit from their features
	-	For example: If an advanced governance module with quadratic voting is used, any module that relies on governance decisions should be subject to the use of that mechanism as well without further development needs.
-   When using modules that integrate external tools, e.g., streaming funds via a protocol like Superfluid, the user may have to input certain additional details required for that module.
    

-Additionally, when specifying which modules to enable, the user can initialize the system with relevant information like:

	-General orchestrator contract Metadata (Identifier/Name, link to, IPFS-hash, etc.)
	-Specific Module information:
	-Initial owner addresses for the Authorizer
	-Funding token address for the FundingManager, Etc.
   
-For security, the system ensures that all modules are initialized before being enabled.
    
-The owners of an orchestrator contract can change, update, pause, and stop their contract flow.
    
-Even in case of pausing or stopping, beneficiaries can still claim any funds they have already earned.
    
-The Factory contract can whitelist specific modules for use, either through a manual approach (for the MVP phase) or automated (via a public library that the users govern)

# Architecture

In this section, we describe the various smart contracts and their functionalities. Together with the general architecture and main interfaces, we also present the structure designed to accommodate future extensions of the overall architecture.

## Overview

The goal of developing the Inverter Protocol is to create a programmable and versatile method for projects or protocols to coordinate the exchange of funds between parties in a flexible and extensible manner. Typically, this is done by organizing around a single wallet-like contract that holds the funds and is managed by a central group of trusted peers. This has a fundamental design limitation around scaling such a coordination system beyond this core group while maintaining security and flexibility.

Instead, we focus on creating a system that tracks relationships and permissions among various stakeholders who may not be part of a tightly defined group. We establish clear interaction points for every party or contract without giving complete control to a single entity. Setting these boundaries allows us to extend trust beyond just one interaction and cover the entire process. Let's take a moment to compare both approaches.

  
In a traditional multi-signature wallet, a trusted group of users comes together and sets rules to manage a shared pool of funds (often using an "x-out-of-y" signature approval model). Once the owners agree, the transfer of funds is carried out.

But this method reaches its limits when implementing more complex flows involving multiple parties and interactions. To address this, we split the system into different parts, which are set up together and linked through a common orchestrator contract. Users can deposit funds to a funding vault, which issues receipts and enforces specific deposit rules. Logic modules can then manage these funds, which perform particular tasks with them. The parameters for these logic modules are determined by a group of addresses (usually the users who set up the system), but even they have clear limits on what they can change. Once funds are ready to leave the system, they are moved to a processing module, where user control is even more restricted.



# Mechian-Wallet
Mechian-Wallet is simply a zk based prover wallet.

Mechian-Wallet integrates a set of smart contracts and microservices, and the ZoKrates zk-snark toolkit,
to enable standard ERC-20 and ERC-721 tokens to be transacted on the Ethereum blockchain with
complete privacy. It is an experimental solution and still being actively developed. We decided to
share our research work in the belief that this will speed adoption of public blockchains. This is
not intended to be a production-ready application and we do not recommend that you use it as such.
If it accelerates your own work, then we are pleased to have helped. We hope that people will feel
motivated to contribute their own ideas and improvements.

**Note that this code has not yet completed a security review and therefore we strongly recommend
that you do not use it in production or to transfer items of material value. We take no
responsibility for any loss you may incur through the use of this code.**


## Security updates

Critical security updates will be listed
[here] If you had previously installed Nightfall prior to one of these security updates, please pull the latest
code, and follow the extra re-installation steps.

## Getting started

These instructions give the most direct path to a working Nightfall setup. The application is
compute-intensive and so a high-end processor is preferred. Depending on your machine, setup can
take one to several hours.

### Installing Mechian-wallet

Clone the Mechian-wallet repository:

```sh
git clone https://github.com/mechainlab/mechian-wallet.git
```

or:

```sh
git clone https://github.com/mechainlab/mechian-wallet.git
```

Enter the directory:

```sh
cd <path/to/nightfall>
```

For Linux users:

- Change permission for the directory

  ```sh
  sudo chmod 777 -R zkp/code/
  ```

- Add the Linux user to docker group to run Docker commands without sudo
  ([read more](https://docs.docker.com/install/linux/linux-postinstall/)). Then log out and enter
  again.

  ```sh
  sudo groupadd docker
  sudo usermod --append --groups docker $USER
  ```

For Mac & Linux users:

Next set a environment variable NPM_TOKEN, value should be a github
personal access token with `:repo` and `:read-package` permission granted.

```sh
export NPM_TOKEN=XXXXX
```

Next pull a compatible Docker image of ZoKrates

```sh
docker pull zokrates/zokrates:0.5.1
```

Next we have to generate the keys and constraint files for Zero Knowledge Proofs
([read more](./zkp/code/README-trusted-setup.md)), this is about 7GB and depends on randomness for
security. This step can take a while, depending on your hardware. Before you start, check once more
that you have provisioned enough memory for Docker, as described above:

```sh
./nightfall-generate-trusted-setup
```

Note that this is an automated run. For an initial installation, select the option to generate all
files. For more information on the MiMC hashing option and further documentation on the setup
process, see [the zkp module documentation](zkp/README.md).

Please be patient - you can check progress in the terminal window and by using `docker stats` in
another terminal.

You just created all the files needed to generate zk-SNARKs. The proving keys, verifying keys and
constraint files will allow you to create hidden tokens, move them under zero knowledge and then
recover them — both for fungible (ERC-20) and non-fungible (ERC-721) tokens.


### To run zkp service unit tests

See [the zkp module documentation](zkp/README.md), "run zkp unit tests".

### To run Nightfall integration test

Be sure to be in the main directory and then open terminal and run

```sh
./nightfall-test
```

- Mac
  - Test suites will open a terminal, where you can see test container's log. This terminal will
    close automatically.
  - configure `Terminal.app` to close window when shell exits `exit`.

## Using the compliance extensions

For details, see the [zkp readme](zkp/README.md). The compliance version requires you to have
selected the correct trusted setup when you ran

```sh
./mechian-wallet-generate-trusted-setup
```

After that, you can start it with

```sh
./mechian-wallet compliance
```

Mechian-wallet will inject test keys into the underlying Nightfall library, warning you that it has done
so. To log in as a compliance administrator, use username `admin`, password `admin`. This is only
for demonstration of course. You should use an appropriate access control system for any other
purpose.

## Using other ERC-20 and ERC-721 contracts

Mechian-wallet will operate with any ERC-20 and ERC-721 compliant contract. The contracts' addresses are
fed into FTokenShield.sol and NFTokenShield.sol respectively during the Truffle migration and cannot
be changed subsequently.

If you wish to use pre-existing ERC-20 and ERC-721 contracts then edit `2_Shield_migration.js` so
that the address of the pre-existing ERC-20 contract is passed to FTokenShield and the address of
the pre-existing ERC-721 contract is passed to NFTokenShield i.e. replace `FToken.address` and
`NFTokenMetadata.address`.

This can also be done from UI, by clicking on the user to go to settings, then clicking on contracts
option in this page. A new shield contract address that has been deployed separately can be provided
here. This new contract will be a replacement for NFTokenShield.sol or FTokenShield.sol. Each of
these contracts currently shields the tokens of an ER721 or ERC20 contract instance respectively.

## Using other networks

The demo mode uses Ganache-cli as a blockchain emulator. This is easier than using a true blockchain
client but has the disadvantage that Ganache-cli doesn't currently support the Whisper protocol,
which Nightfall uses for exchanging secrets between sender and receiver. Accordingly we've written a
Whisper stub, which will emulate whisper for participants who are all on the same node server. If
you want to run across multiple blockchain nodes and server instances then replace all occurrences
of the words `whisper-controller-stub` with `whisper-controller` in the code — but you will need to
use Geth rather than Ganache-cli and construct an appropriate Docker container to replace the
Ganache one we provide.

## Acknowledgements

Team Mechian-wallet thanks those who have indirectly contributed to it, with the ideas and tools that
they have shared with the community:

- [ZoKrates](https://hub.docker.com/r/michaelconnor/zok)
- [Nightfall](https://github.com/EYBlockchain/nightfall)
- [Libsnark](https://github.com/scipr-lab/libsnark)
- [Zcash](https://github.com/zcash/zcash)
- [GM17](https://eprint.iacr.org/2017/540.pdf)
- [0xcert](https://github.com/0xcert/ethereum-erc721/)
- [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC20/ERC20.sol)

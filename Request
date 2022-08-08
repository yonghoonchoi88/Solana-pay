import { clusterApiUrl, Connection, Keypair, PublicKey, Transaction } from '@solana/web3.js';
import BigNumber from 'bignumber.js';
import { createTransferCheckedInstruction, getAccount, getAssociatedTokenAddress, getMint } from '@solana/spl-token';
import { TEN } from '@solana/pay';

const splToken = new PublicKey(process.env.USDC_MINT);
const MERCHANT_WALLET = new PublicKey(process.env.MERCHANT_WALLET);

const post = async (request, response) => {
    // Account provided in the transaction request body by the wallet.
    const accountField = request.body?.account;
    if (!accountField) throw new Error('missing account');

    const sender = new PublicKey(accountField);

    // create spl transfer instruction
    const splTransferIx = await createSplTransferIx(sender, connection);

    // create the transaction
    const transaction = new Transaction();

    // add the instruction to the transaction
    transaction.add(splTransferIx);

    // Serialize and return the unsigned transaction.
    const serializedTransaction = transaction.serialize({
        verifySignatures: false,
        requireAllSignatures: false,
    });

    const base64Transaction = serializedTransaction.toString('base64');
    const message = 'Thank you for your purchase of ExiledApe #518';

    response.status(200).send({ transaction: base64Transaction, message });
};

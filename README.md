{
  "name": "mp-server",
  "version": "1.0.0",
  "main": "index.js",
  "type": "module",
  "dependencies": {
    "express": "^4.18.2",
    "mercadopago": "^1.5.4"
  }
}
import express from "express";
import mercadopago from "mercadopago";
import cors from "cors";

const app = express();
app.use(express.json());
app.use(cors());

mercadopago.configure({
    access_token: process.env.MP_ACCESS_TOKEN
});

// GERAR PIX
app.post("/gerar-pix", async (req, res) => {
    try {
        const { valor, descricao } = req.body;

        const pagamento = await mercadopago.payment.create({
            transaction_amount: Number(valor),
            description: descricao,
            payment_method_id: "pix"
        });

        const data = pagamento.body.point_of_interaction.transaction_data;

        res.json({
            qrcode: data.qr_code,
            base64: data.qr_code_base64
        });

    } catch (err) {
        res.status(400).json({ erro: err.message });
    }
});

// WEBHOOK
app.post("/webhook", (req, res) => {
    console.log("Webhook recebido:", req.body);
    res.sendStatus(200);
});

app.listen(3000, () => console.log("Servidor rodando."));

MP_ACCESS_TOKEN=sua-chave-secreta-aqui
npm install
node index.js
MP_ACCESS_TOKEN = sua chave secreta

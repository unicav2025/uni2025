import React, { useState, useEffect } from "react";
import { initializeApp } from "firebase/app";
import {
  getFirestore,
  collection,
  addDoc,
  onSnapshot,
  serverTimestamp,
} from "firebase/firestore";
import {
  getStorage,
  ref,
  uploadBytes,
  getDownloadURL,
} from "firebase/storage";

// Config do Firebase (substituir pelas suas credenciais)
const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_PROJETO.firebaseapp.com",
  projectId: "SEU_PROJETO",
  storageBucket: "SEU_PROJETO.appspot.com",
  messagingSenderId: "SEU_ID",
  appId: "SUA_APP_ID",
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const storage = getStorage(app);

export default function LandingPage() {
  const [nome, setNome] = useState("vi");
  const [parcela, setParcela] = useState("1 parcela");
  const [comprovante, setComprovante] = useState(null);
  const [registros, setRegistros] = useState([]);

  useEffect(() => {
    const unsub = onSnapshot(collection(db, "registros"), (snapshot) => {
      const dados = snapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }));
      setRegistros(dados);
    });
    return () => unsub();
  }, []);

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!comprovante) return alert("Anexe um comprovante!");

    try {
      // Upload arquivo no Storage
      const storageRef = ref(storage, `comprovantes/${Date.now()}_${comprovante.name}`);
      await uploadBytes(storageRef, comprovante);
      const fileURL = await getDownloadURL(storageRef);

      // Salvar no Firestore
      await addDoc(collection(db, "registros"), {
        nome,
        parcela,
        comprovante: fileURL,
        data: serverTimestamp(),
      });

      setComprovante(null);
      e.target.reset();
    } catch (error) {
      console.error("Erro ao salvar: ", error);
    }
  };

  return (
    <div className="min-h-screen bg-gray-100 flex flex-col items-center p-6">
      <h1 className="text-2xl font-bold mb-6">Envio de Comprovantes</h1>

      {/* Formulário */}
      <form
        onSubmit={handleSubmit}
        className="bg-white p-6 rounded-2xl shadow-md w-full max-w-md mb-8"
      >
        <label className="block mb-2">Nome:</label>
        <select
          value={nome}
          onChange={(e) => setNome(e.target.value)}
          className="w-full border p-2 rounded mb-4"
        >
          <option value="vi">Vi</option>
          <option value="nati">Nati</option>
          <option value="emi">Emi</option>
        </select>

        <label className="block mb-2">Parcela:</label>
        <select
          value={parcela}
          onChange={(e) => setParcela(e.target.value)}
          className="w-full border p-2 rounded mb-4"
        >
          <option>1 parcela</option>
          <option>2 parcelas</option>
          <option>única parcela</option>
        </select>

        <label className="block mb-2">Comprovante:</label>
        <input
          type="file"
          accept="image/*,.pdf"
          onChange={(e) => setComprovante(e.target.files[0])}
          className="w-full border p-2 rounded mb-4"
        />

        <button
          type="submit"
          className="bg-green-600 text-white px-4 py-2 rounded-xl hover:bg-green-700"
        >
          Salvar
        </button>
      </form>

      {/* Planilha */}
      <h2 className="text-xl font-semibold mb-4">Planilha de Envio</h2>
      <div className="w-full max-w-3xl overflow-x-auto">
        <table className="w-full border bg-white rounded-xl shadow-md">
          <thead>
            <tr className="bg-gray-200">
              <th className="p-2 border">Nome</th>
              <th className="p-2 border">Parcela</th>
              <th className="p-2 border">Data</th>
              <th className="p-2 border">Comprovante</th>
            </tr>
          </thead>
          <tbody>
            {registros.map((r, i) => (
              <tr key={i}>
                <td className="p-2 border">{r.nome}</td>
                <td className="p-2 border">{r.parcela}</td>
                <td className="p-2 border">
                  {r.data?.toDate ? r.data.toDate().toLocaleString() : "-"}
                </td>
                <td className="p-2 border text-center">
                  <a
                    href={r.comprovante}
                    target="_blank"
                    rel="noopener noreferrer"
                    className="text-blue-600 underline"
                  >
                    Ver arquivo
                  </a>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
}

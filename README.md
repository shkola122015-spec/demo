import React, { useState, useEffect } from "react"; 
import { motion } from "framer-motion"; 
import { initializeApp } from "firebase/app"; 
import { getDatabase, ref, onValue, set, push } from "firebase/database";

// Firebase конфигурация (сюда надо вставить свои ключи из Firebase Console) 
const firebaseConfig = {
  apiKey: "AIzaSyCn9U2_DhkcccpnXeIGm0DjOxy3-Y8uRh8",
  authDomain: "kaef-c4a52.firebaseapp.com",
  projectId: "kaef-c4a52",
  storageBucket: "kaef-c4a52.firebasestorage.app",
  messagingSenderId: "954182870965",
  appId: "1:954182870965:web:63b7fdcb11eef45cd39bfc",
  measurementId: "G-069TQBY3M5"
};


const app = initializeApp(firebaseConfig); 
const db = getDatabase(app);

export default function MountainGame() { 
  const [players, setPlayers] = useState([]); 
  const [newPlayer, setNewPlayer] = useState("");

// Загружаем игроков из базы 
useEffect(() => { 
  const playersRef = ref(db, "players"); 
  onValue(playersRef, (snapshot) => { 
    const data = snapshot.val() || {}; 
    const loadedPlayers = Object.keys(data).map((key) => ({ id: key, ...data[key] })); 
    setPlayers(loadedPlayers); 
  }); 
}, []);

// Добавляем игрока в базу 
const addPlayer = () => { 
  if (newPlayer.trim() !== "") { 
    const playersRef = ref(db, "players"); 
    const newRef = push(playersRef); 
    set(newRef, { name: newPlayer, steps: 0 }); 
    setNewPlayer(""); 
  } 
};

// Прибавляем шаг игроку в базе 
const moveUp = (playerId, currentSteps) => { 
  const playerRef = ref(db, players/${playerId}); 
  set(playerRef, { name: players.find(p => p.id === playerId).name, steps: currentSteps + 1 }); 
};

return ( 
  <div className="flex flex-col items-center p-4 bg-blue-50 min-h-screen"> 
    <h1 className="text-2xl font-bold mb-4">Игра: Восхождение на гору 🏔️</h1>

{/* Добавление игрока */}
    <div className="flex gap-2 mb-4">
      <input
        className="border p-2 rounded"
        placeholder="Имя игрока"
        value={newPlayer}
        onChange={(e) => setNewPlayer(e.target.value)}
      />
      <button
        className="bg-green-500 text-white px-4 py-2 rounded"
        onClick={addPlayer}
      >
        Добавить
      </button>
    </div>

  {/* Список игроков и прогресс */}
    <div className="w-full max-w-md">
      {players.map((player) => (
        <div key={player.id} className="flex justify-between items-center bg-white rounded p-2 mb-2 shadow">
          <span>{player.name}</span>
          <div className="flex items-center gap-2">
            <motion.div
              initial={{ y: 0 }}
              animate={{ y: -player.steps * 20 }}
              className="w-6 h-6 bg-yellow-400 rounded-full"
            ></motion.div>
            <button
              className="bg-blue-500 text-white px-2 py-1 rounded"
              onClick={() => moveUp(player.id, player.steps)}
            >
              +
            </button>
          </div>
        </div>
      ))}
    </div>
  </div>

); 
}

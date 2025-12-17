import { useEffect, useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";

const DRINKS = {
  can: { label: "Can (375ml)", ml: 375 },
  bottle600: { label: "Bottle (600ml)", ml: 600 },
  bottle2l: { label: "Bottle (2L)", ml: 2000 },
  fountain: { label: "Fountain", ml: 500 },
};

export default function PepsiMaxCounter2026() {
  const STORAGE_KEY = "pepsi-max-2026-log";
  const [log, setLog] = useState([]);

  useEffect(() => {
    const saved = localStorage.getItem(STORAGE_KEY);
    if (saved) setLog(JSON.parse(saved));
  }, []);

  useEffect(() => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(log));
  }, [log]);

  const addDrink = (type) => {
    setLog((prev) => [
      ...prev,
      { type, ml: DRINKS[type].ml, date: new Date().toISOString() },
    ]);
  };

  const totalMl = log.reduce((sum, d) => sum + d.ml, 0);
  const totalCount = log.length;

  const monthlyStats = log.reduce((acc, d) => {
    const month = new Date(d.date).toLocaleString("default", {
      month: "long",
    });
    acc[month] = acc[month] || { count: 0, ml: 0 };
    acc[month].count += 1;
    acc[month].ml += d.ml;
    return acc;
  }, {});

  return (
    <div className="min-h-screen bg-neutral-50 flex items-center justify-center p-4">
      <motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }}>
        <Card className="w-full max-w-md rounded-2xl shadow-md">
          <CardContent className="p-6 space-y-6">
            <div className="text-center">
              <h1 className="text-2xl font-semibold">🥤 Pepsi Max Replay</h1>
              <p className="text-sm text-neutral-500">2026</p>
            </div>

            <div className="grid grid-cols-2 gap-3">
              {Object.entries(DRINKS).map(([key, d]) => (
                <Button
                  key={key}
                  className="rounded-xl"
                  onClick={() => addDrink(key)}
                >
                  + {d.label}
                </Button>
              ))}
            </div>

            <div className="text-center space-y-1">
              <div className="text-3xl font-bold">{totalCount}</div>
              <div className="text-sm text-neutral-500">
                Total drinks · {(totalMl / 1000).toFixed(1)}L
              </div>
            </div>

            <div className="space-y-2">
              <h2 className="text-sm font-semibold text-neutral-600">
                Monthly Replay
              </h2>
              {Object.entries(monthlyStats).map(([month, s]) => (
                <div
                  key={month}
                  className="flex justify-between text-sm bg-neutral-100 rounded-xl px-3 py-2"
                >
                  <span>{month}</span>
                  <span>
                    {s.count} · {(s.ml / 1000).toFixed(1)}L
                  </span>
                </div>
              ))}
            </div>

            <p className="text-xs text-neutral-400 text-center">
              Auto-saves · Local only · Replay-style totals
            </p>
          </CardContent>
        </Card>
      </motion.div>
    </div>
  );
}


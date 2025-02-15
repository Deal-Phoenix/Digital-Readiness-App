# Digital-Readiness-App
import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Progress } from "@/components/ui/progress";
import { BarChart, Bar, XAxis, YAxis, Tooltip, ResponsiveContainer } from "recharts";

const categories = [
  {
    name: "Technologie & Infrastruktur",
    questions: [
      "Nutzen Sie eine hybride Cloud-Strategie oder On-Premise?",
      "Sind Ihre Systeme API-fähig für Datenintegrationen?",
    ],
  },
  {
    name: "Datenmanagement & Analytics",
    questions: [
      "Haben Sie eine zentrale Datenplattform (Data Warehouse oder Data Lake)?",
      "Sind Ihre Daten in strukturierte & unstrukturierte Daten kategorisiert?",
    ],
  },
  {
    name: "Prozesse & Automatisierung",
    questions: [
      "Nutzen Sie RPA für wiederkehrende Prozesse?",
      "Wie hoch ist der Automatisierungsgrad Ihres Unternehmens?",
    ],
  },
  {
    name: "AI & Machine Learning Readiness",
    questions: [
      "Gibt es eine AI-Strategie oder erste PoCs?",
      "Werden AI-Modelle zur Entscheidungsunterstützung genutzt?",
    ],
  },
];

export default function DigitalReadinessApp() {
  const [responses, setResponses] = useState(
    categories.map((cat) => cat.questions.map(() => 3))
  );

  const handleResponseChange = (catIndex, qIndex, value) => {
    setResponses((prevResponses) => {
      const newResponses = prevResponses.map((cat, i) =>
        i === catIndex ? cat.map((val, j) => (j === qIndex ? value : val)) : cat
      );
      return newResponses;
    });
  };

  const calculateScore = () => {
    const totalQuestions = responses.flat().length;
    const totalScore = responses.flat().reduce((sum, val) => sum + val, 0);
    return Math.round((totalScore / (totalQuestions * 5)) * 100);
  };

  const chartData = categories.map((category, index) => ({
    name: category.name,
    score:
      responses[index].reduce((a, b) => a + b, 0) / responses[index].length,
  }));

  return (
    <div className="p-6 max-w-4xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">Digital Readiness Assessment</h1>
      {categories.map((category, catIndex) => (
        <Card key={category.name} className="mb-4">
          <CardContent>
            <h2 className="text-xl font-semibold mb-2">{category.name}</h2>
            {category.questions.map((question, qIndex) => (
              <div key={question} className="mb-2">
                <p>{question}</p>
                <input
                  type="range"
                  min="1"
                  max="5"
                  value={responses[catIndex][qIndex]}
                  onChange={(e) =>
                    handleResponseChange(catIndex, qIndex, Number(e.target.value))
                  }
                  className="w-full"
                />
              </div>
            ))}
          </CardContent>
        </Card>
      ))}
      <Button onClick={() => alert(`Ihr Digital Readiness Score: ${calculateScore()}%`)}>
        Ergebnisse anzeigen
      </Button>
      <Progress value={calculateScore()} className="mt-4" />
      <ResponsiveContainer width="100%" height={300} className="mt-4">
        <BarChart data={chartData} layout="vertical">
          <XAxis type="number" domain={[0, 5]} hide />
          <YAxis dataKey="name" type="category" width={200} />
          <Tooltip />
          <Bar dataKey="score" fill="#82ca9d" />
        </BarChart>
      </ResponsiveContainer>
    </div>
  );
}

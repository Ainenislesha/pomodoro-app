import { useState, useEffect, useRef } from "react";
import { Button } from "@/components/ui/button";
import { Progress } from "@/components/ui/progress";
import { Input } from "@/components/ui/input";
import { Card, CardContent } from "@/components/ui/card";
import { motion } from "framer-motion";

export default function PomodoroApp() {
  const [pomoTime, setPomoTime] = useState(25 * 60);
  const [breakTime, setBreakTime] = useState(5 * 60);
  const [timeLeft, setTimeLeft] = useState(pomoTime);
  const [isRunning, setIsRunning] = useState(false);
  const [isBreak, setIsBreak] = useState(false);
  const [streaks, setStreaks] = useState(0);

  const audioRef = useRef(null);
  const videoRef = useRef(null);

  useEffect(() => {
    if (!isRunning) return;
    const timer = setInterval(() => {
      setTimeLeft((prev) => {
        if (prev === 0) {
          if (isBreak) {
            setIsBreak(false);
            setTimeLeft(pomoTime);
            if (videoRef.current) videoRef.current.pause();
          } else {
            setIsBreak(true);
            setTimeLeft(breakTime);
            setStreaks((prevStreaks) => prevStreaks + 1);
            if (videoRef.current) {
              videoRef.current.currentTime = 0;
              videoRef.current.play();
            }
          }
          return;
        }
        return prev - 1;
      });
    }, 1000);
    return () => clearInterval(timer);
  }, [isRunning, isBreak, pomoTime, breakTime]);

  useEffect(() => {
    if (audioRef.current) {
      if (isRunning && !isBreak) {
        audioRef.current.play();
      } else {
        audioRef.current.pause();
      }
    }
  }, [isRunning, isBreak]);

  return (
    <div className="flex flex-col items-center justify-center min-h-screen bg-gradient-to-r from-purple-400 via-pink-500 to-red-500 text-white p-5">
      <Card className="w-full max-w-lg p-5 text-center">
        <h1 className="text-3xl font-bold mb-4">{isBreak ? "Break Time" : "Pomodoro Timer"}</h1>
        <motion.div animate={{ scale: [1, 1.1, 1] }}>
          <p className="text-6xl font-semibold">{Math.floor(timeLeft / 60)}:{(timeLeft % 60).toString().padStart(2, "0")}</p>
        </motion.div>
        <Progress value={(timeLeft / (isBreak ? breakTime : pomoTime)) * 100} className="my-4" />
        <div className="flex gap-4 justify-center">
          <Button onClick={() => setIsRunning(!isRunning)}>{isRunning ? "Pause" : "Start"}</Button>
        </div>
      </Card>
      <Card className="w-full max-w-lg p-5 mt-5 text-center">
        <h2 className="text-xl font-semibold">Settings</h2>
        <div className="flex gap-2 justify-center mt-3">
          <Input type="number" value={pomoTime / 60} onChange={(e) => setPomoTime(e.target.value * 60)} placeholder="Pomodoro (min)" className="w-24" />
          <Input type="number" value={breakTime / 60} onChange={(e) => setBreakTime(e.target.value * 60)} placeholder="Break (min)" className="w-24" />
        </div>
      </Card>
      <Card className="w-full max-w-lg p-5 mt-5 text-center">
        <h2 className="text-xl font-semibold">Streaks</h2>
        <p className="text-3xl font-bold">🔥 {streaks}</p>
      </Card>
      <audio ref={audioRef} src="/mnt/data/Study Music Alpha Waves_ Relaxing Studying Music, Brain Power, Focus Concentration Music, ☯161 [ ezmp3.co ].mp3" loop />
      <video ref={videoRef} src="/mnt/data/videoplayback.mp4" className="w-full max-w-lg mt-5" controls />
    </div>
  );
}
# pomodoro-app

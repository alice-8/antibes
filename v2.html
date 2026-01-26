import { useRef, useEffect, useState } from "react";
import { allColors, shiftColor } from "@/lib/antibes-colors";
import { useToast } from "@/hooks/use-toast";

interface Dot {
  x: number;
  y: number;
  baseX: number;
  baseY: number;
  size: number;
  color: string;
  vx: number;
  vy: number;
}

interface AudioVisualizerProps {
  isMicOn: boolean;
  onVolumeChange: (vol: number) => void;
  onClap: () => void;
  colorShift: number;
  handPosition?: { x: number; y: number; active: boolean };
}

export function AudioVisualizer({
  isMicOn,
  onVolumeChange,
  onClap,
  colorShift,
  handPosition,
}: AudioVisualizerProps) {
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const audioContextRef = useRef<AudioContext | null>(null);
  const analyserRef = useRef<AnalyserNode | null>(null);
  const dataArrayRef = useRef<Uint8Array | null>(null);
  const sourceRef = useRef<MediaStreamAudioSourceNode | null>(null);
  const streamRef = useRef<MediaStream | null>(null);
  const rafRef = useRef<number>();
  const dotsRef = useRef<Dot[]>([]);
  const mouseRef = useRef({ x: -1000, y: -1000 });
  
  const { toast } = useToast();

  // Initialize Audio
  useEffect(() => {
    const cleanupAudio = () => {
      sourceRef.current?.disconnect();
      sourceRef.current = null;
      analyserRef.current?.disconnect();
      analyserRef.current = null;
      dataArrayRef.current = null;
      if (audioContextRef.current && audioContextRef.current.state !== 'closed') {
        audioContextRef.current.close();
      }
      audioContextRef.current = null;
      if (streamRef.current) {
        streamRef.current.getTracks().forEach(track => track.stop());
        streamRef.current = null;
      }
    };

    if (!isMicOn) {
      cleanupAudio();
      return;
    }

    const initAudio = async () => {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        streamRef.current = stream;
        const audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)();
        const analyser = audioCtx.createAnalyser();
        const source = audioCtx.createMediaStreamSource(stream);
        
        analyser.fftSize = 256;
        source.connect(analyser);
        
        audioContextRef.current = audioCtx;
        analyserRef.current = analyser;
        sourceRef.current = source;
        dataArrayRef.current = new Uint8Array(analyser.frequencyBinCount);
      } catch (err) {
        console.error("Error accessing microphone:", err);
        toast({
          title: "Microphone Access Denied",
          description: "Please allow microphone access to use audio reactivity.",
          variant: "destructive",
        });
      }
    };

    initAudio();

    return () => {
      cleanupAudio();
    };
  }, [isMicOn, toast]);

  // Initialize Dots
  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;

    const resizeCanvas = () => {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      initDots();
    };

    const initDots = () => {
      const dots: Dot[] = [];
      const numDots = 2000;
      
      for (let i = 0; i < numDots; i++) {
        const x = Math.random() * canvas.width;
        const y = Math.random() * canvas.height;
        dots.push({
          x,
          y,
          baseX: x,
          baseY: y,
          size: Math.random() * 3 + 1,
          color: allColors[Math.floor(Math.random() * allColors.length)],
          vx: 0,
          vy: 0,
        });
      }
      dotsRef.current = dots;
    };

    resizeCanvas();
    window.addEventListener("resize", resizeCanvas);
    
    const handleMouseMove = (e: MouseEvent) => {
      mouseRef.current = { x: e.clientX, y: e.clientY };
    };
    window.addEventListener("mousemove", handleMouseMove);

    return () => {
      window.removeEventListener("resize", resizeCanvas);
      window.removeEventListener("mousemove", handleMouseMove);
    };
  }, []);

  // Animation Loop
  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext("2d");
    if (!ctx) return;

    let lastVolume = 0;

    const animate = () => {
      ctx.fillStyle = "rgba(10, 20, 40, 0.2)"; // Trail effect
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Audio Processing
      let volume = 0;
      if (analyserRef.current && dataArrayRef.current) {
        analyserRef.current.getByteFrequencyData(dataArrayRef.current);
        const average = dataArrayRef.current.reduce((a, b) => a + b) / dataArrayRef.current.length;
        volume = average / 255; // Normalized 0-1
      }
      
      // Clap detection logic (sudden spike)
      if (volume > 0.3 && volume > lastVolume * 1.5) {
        onClap();
      }
      lastVolume = Math.max(0.01, volume);
      onVolumeChange(volume);

      // Dot Physics
      dotsRef.current.forEach((dot) => {
        // 1. Mouse Repulsion
        const dx = mouseRef.current.x - dot.x;
        const dy = mouseRef.current.y - dot.y;
        const distance = Math.sqrt(dx * dx + dy * dy);
        const maxDist = 150;

        if (distance < maxDist) {
          const forceDirectionX = dx / distance;
          const forceDirectionY = dy / distance;
          const force = (maxDist - distance) / maxDist;
          const directionX = forceDirectionX * force * 5;
          const directionY = forceDirectionY * force * 5;

          dot.vx -= directionX;
          dot.vy -= directionY;
        }

        // 1b. Hand/Camera Motion Repulsion
        if (handPosition && handPosition.active) {
          const hdx = handPosition.x - dot.x;
          const hdy = handPosition.y - dot.y;
          const hDistance = Math.sqrt(hdx * hdx + hdy * hdy);
          const handMaxDist = 200;

          if (hDistance < handMaxDist) {
            const hForceX = hdx / hDistance;
            const hForceY = hdy / hDistance;
            const hForce = (handMaxDist - hDistance) / handMaxDist;
            dot.vx -= hForceX * hForce * 6;
            dot.vy -= hForceY * hForce * 6;
          }
        }

        // 2. Audio "Dance" (Jitter based on volume)
        if (volume > 0.05) {
          dot.vx += (Math.random() - 0.5) * volume * 2;
          dot.vy += (Math.random() - 0.5) * volume * 2;
        }

        // 3. Return to base (Spring force)
        const spring = 0.05;
        const dxBase = dot.baseX - dot.x;
        const dyBase = dot.baseY - dot.y;
        
        dot.vx += dxBase * spring;
        dot.vy += dyBase * spring;

        // 4. Friction
        const friction = 0.90;
        dot.vx *= friction;
        dot.vy *= friction;

        // 5. Update Position
        dot.x += dot.vx;
        dot.y += dot.vy;

        // 6. Draw
        const displayColor = shiftColor(dot.color, colorShift);
        ctx.beginPath();
        ctx.arc(dot.x, dot.y, dot.size + (volume * 5), 0, Math.PI * 2);
        ctx.fillStyle = displayColor;
        ctx.fill();
      });

      rafRef.current = requestAnimationFrame(animate);
    };

    animate();

    return () => {
      if (rafRef.current) cancelAnimationFrame(rafRef.current);
    };
  }, [colorShift, onClap, onVolumeChange]);

  return (
    <canvas
      ref={canvasRef}
      className="fixed inset-0 w-full h-full cursor-crosshair touch-none"
    />
  );
}

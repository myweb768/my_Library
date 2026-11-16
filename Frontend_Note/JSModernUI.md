# JSMorderUI: React-এর সাথে GSAP, Three.js ও Chart.js

> এই ডকুমেন্ট পড়ে সরাসরি কাজ শুরু করা যাবে। প্রতিটি অংশে আগে **থিওরি**, তারপর **React-এ কোড**, শেষে **সাধারণ ভুল** দেওয়া আছে।

---

## সূচিপত্র

1. [কোনটা কী কাজে লাগে](#1-কোনটা-কী-কাজে-লাগে)
2. [প্রজেক্ট সেটআপ](#2-প্রজেক্ট-সেটআপ)
3. [শেখার রোডম্যাপ](#3-শেখার-রোডম্যাপ)
4. [GSAP](#4-gsap)
5. [Three.js](#5-threejs)
6. [Chart.js](#6-chartjs)
7. [তিনটাকে একসাথে ব্যবহার (Integration)](#7-তিনটাকে-একসাথে-ব্যবহার)
8. [চিটশিট ও সাধারণ ভুল](#8-চিটশিট-ও-সাধারণ-ভুল)
9. [প্র্যাকটিস প্রজেক্ট আইডিয়া](#9-প্র্যাকটিস-প্রজেক্ট-আইডিয়া)

---

## 1. কোনটা কী কাজে লাগে

| লাইব্রেরি | কাজ | উদাহরণ |
|---|---|---|
| **GSAP** | অ্যানিমেশন (UI, স্ক্রল, টাইমলাইন) | fade-in, slide, scroll-এ এলিমেন্ট আসা, hero animation |
| **Three.js** | ৩D গ্রাফিক্স (WebGL) | ঘুরন্ত ৩D মডেল, পার্টিকল ব্যাকগ্রাউন্ড, product viewer |
| **Chart.js** | ডেটা চার্ট (Canvas) | bar, line, pie, doughnut, radar চার্ট |

তিনটাই আলাদা কাজের। একটা প্রজেক্টে তিনটা একসাথে চালানো যায় (যেমন ড্যাশবোর্ডে GSAP দিয়ে কার্ড আসবে, Chart.js দিয়ে চার্ট হবে, পাশে Three.js-এর ৩D হিরো)।

---

## 2. প্রজেক্ট সেটআপ

```bash
npm create vite@latest jsmodernui -- --template react
cd jsmodernui
npm install

# GSAP
npm install gsap @gsap/react

# Three.js (সরাসরি) + React Three Fiber (React-এর জন্য সহজ উপায়)
npm install three @react-three/fiber @react-three/drei

# Chart.js + React wrapper
npm install chart.js react-chartjs-2

npm run dev
```

**ফোল্ডার স্ট্রাকচার (প্রস্তাবিত):**

```
src/
  components/
    gsap/        FadeIn.jsx, ScrollSection.jsx
    three/       Cube.jsx, Scene.jsx
    charts/      SalesChart.jsx, CategoryChart.jsx
  App.jsx
  main.jsx
```

---

## 3. শেখার রোডম্যাপ

**সপ্তাহ ১: GSAP**
- `gsap.to / from / fromTo`
- easing, stagger
- Timeline
- ScrollTrigger
- React-এ `useGSAP`

**সপ্তাহ ২: Chart.js**
- চার্টের গঠন (type, data, options)
- Bar / Line / Doughnut
- options কাস্টমাইজ (scales, plugins, tooltip)
- ডাইনামিক ডেটা আপডেট

**সপ্তাহ ৩-৪: Three.js**
- Scene, Camera, Renderer
- Geometry, Material, Mesh
- Light
- Animation loop
- React Three Fiber (`Canvas`, `useFrame`, drei)
- মডেল লোড (GLTF)

**শেষে:** তিনটা মিলিয়ে একটা ছোট প্রজেক্ট বানান।

---

## 4. GSAP

### 4.1 থিওরি: মূল ধারণা

GSAP মূলত একটা এলিমেন্টের প্রপার্টি (position, opacity, scale, rotation ইত্যাদি) সময়ের সাথে বদলায়।

| কনসেপ্ট | মানে |
|---|---|
| **Tween** | একটা অ্যানিমেশন (A থেকে B) |
| `gsap.to()` | বর্তমান অবস্থা থেকে নির্দিষ্ট মানে যাও |
| `gsap.from()` | নির্দিষ্ট মান থেকে বর্তমান অবস্থায় এসো |
| `gsap.fromTo()` | শুরু ও শেষ দুটোই নিজে ঠিক করো |
| `gsap.set()` | সাথে সাথে মান বসাও (অ্যানিমেশন ছাড়া) |
| **Easing** | গতির ধরন (`power2.out`, `back.out(1.7)`, `elastic.out`) |
| **Stagger** | একাধিক এলিমেন্ট একটার পর একটা শুরু হওয়া |
| **Timeline** | অনেকগুলো অ্যানিমেশন ক্রমানুসারে সাজানো |
| **ScrollTrigger** | স্ক্রল অনুযায়ী অ্যানিমেশন চালানো |

**সাধারণ প্রপার্টি:** `x`, `y`, `scale`, `rotation`, `opacity`, `duration`, `delay`, `ease`, `stagger`, `repeat`, `yoyo`

### 4.2 প্রথম অ্যানিমেশন (React-এ)

React-এ সবসময় `useGSAP` হুক ব্যবহার করুন। এটা নিজে থেকে cleanup করে দেয় (`useEffect`-এর মতো হাতে cleanup লিখতে হয় না)।

```jsx
// src/components/gsap/FadeIn.jsx
import { useRef } from "react";
import gsap from "gsap";
import { useGSAP } from "@gsap/react";

gsap.registerPlugin(useGSAP);

export default function FadeIn() {
  const boxRef = useRef(null);

  useGSAP(() => {
    gsap.from(boxRef.current, {
      opacity: 0,
      y: 50,
      duration: 1,
      ease: "power3.out",
    });
  }, []);

  return <div ref={boxRef} className="box">Hello GSAP</div>;
}
```

### 4.3 `scope` দিয়ে একাধিক এলিমেন্ট (stagger)

`scope` দিলে ভেতরের CSS selector শুধু ওই কম্পোনেন্টের মধ্যেই কাজ করে।

```jsx
import { useRef } from "react";
import gsap from "gsap";
import { useGSAP } from "@gsap/react";

export default function Cards() {
  const container = useRef(null);

  useGSAP(
    () => {
      gsap.from(".card", {
        opacity: 0,
        y: 40,
        duration: 0.8,
        stagger: 0.15, // প্রতিটা কার্ড 0.15s পরপর
        ease: "power2.out",
      });
    },
    { scope: container }
  );

  return (
    <div ref={container} className="grid">
      <div className="card">১</div>
      <div className="card">২</div>
      <div className="card">৩</div>
    </div>
  );
}
```

### 4.4 Timeline

```jsx
useGSAP(
  () => {
    const tl = gsap.timeline({ defaults: { duration: 0.8, ease: "power2.out" } });

    tl.from(".title", { opacity: 0, y: -30 })
      .from(".subtitle", { opacity: 0, y: 20 }, "-=0.4") // আগেরটা শেষ হওয়ার 0.4s আগে শুরু
      .from(".btn", { scale: 0, ease: "back.out(1.7)" });
  },
  { scope: container }
);
```

### 4.5 ইভেন্টে অ্যানিমেশন (`contextSafe`)

useGSAP-এর বাইরে (যেমন onClick-এ) অ্যানিমেশন চালাতে `contextSafe` লাগে, না হলে cleanup ঠিকমতো হয় না।

```jsx
export default function ClickBox() {
  const container = useRef(null);
  const boxRef = useRef(null);

  const { contextSafe } = useGSAP({ scope: container });

  const onClick = contextSafe(() => {
    gsap.to(boxRef.current, { rotation: "+=360", duration: 1 });
  });

  return (
    <div ref={container}>
      <div ref={boxRef} className="box" onClick={onClick}>Click me</div>
    </div>
  );
}
```

### 4.6 ScrollTrigger

```jsx
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useGSAP } from "@gsap/react";

gsap.registerPlugin(useGSAP, ScrollTrigger);

export default function ScrollSection() {
  const container = useRef(null);

  useGSAP(
    () => {
      gsap.from(".item", {
        opacity: 0,
        y: 80,
        stagger: 0.2,
        scrollTrigger: {
          trigger: container.current,
          start: "top 80%",   // এলিমেন্টের top যখন স্ক্রিনের 80% জায়গায় আসবে
          end: "bottom 20%",
          toggleActions: "play none none reverse",
          // scrub: true,     // স্ক্রলের সাথে সাথে অ্যানিমেশন চলবে
          // pin: true,       // সেকশন আটকে রাখবে
          // markers: true,   // ডিবাগের জন্য দাগ দেখাবে
        },
      });
    },
    { scope: container }
  );

  return (
    <section ref={container}>
      <div className="item">A</div>
      <div className="item">B</div>
      <div className="item">C</div>
    </section>
  );
}
```

### 4.7 GSAP: সাধারণ ভুল

- `useEffect` দিয়ে লিখে cleanup না করা: `useGSAP` ব্যবহার করুন।
- selector ব্যবহার করে `scope` না দেওয়া: অন্য কম্পোনেন্টের এলিমেন্টও ধরে ফেলতে পারে।
- অ্যানিমেশনের জন্য `width`/`left`/`top` বদলানো: `x`, `y`, `scale` ব্যবহার করুন (পারফরম্যান্স ভালো)।
- ScrollTrigger-এ কন্টেন্ট পরে লোড হলে পজিশন ভুল হয়: `ScrollTrigger.refresh()` কল করুন।
- React StrictMode-এ dev-এ দুইবার চলা স্বাভাবিক: `useGSAP` এটা ঠিক করে দেয়।

---

## 5. Three.js

### 5.1 থিওরি: মূল ধারণা

Three.js-এ ৩D দৃশ্য দেখাতে ৩টা জিনিস অবশ্যই লাগে:

```
Scene (মঞ্চ) + Camera (চোখ) + Renderer (পর্দায় আঁকে)
```

| কনসেপ্ট | মানে |
|---|---|
| **Scene** | সব ৩D অবজেক্টের কন্টেইনার |
| **Camera** | আমরা কোথা থেকে দেখছি (`PerspectiveCamera` সবচেয়ে বেশি ব্যবহৃত) |
| **Renderer** | `WebGLRenderer`: Scene ও Camera নিয়ে `<canvas>`-এ আঁকে |
| **Geometry** | আকৃতি (`BoxGeometry`, `SphereGeometry`, `TorusGeometry`) |
| **Material** | চেহারা/রং/সারফেস (`MeshBasicMaterial`, `MeshStandardMaterial`) |
| **Mesh** | Geometry + Material = দৃশ্যমান অবজেক্ট |
| **Light** | আলো (`AmbientLight`, `DirectionalLight`, `PointLight`). `MeshStandardMaterial`-এ আলো ছাড়া কালো দেখায় |
| **Animation loop** | প্রতি ফ্রেমে (প্রায় ৬০ বার/সেকেন্ড) দৃশ্য নতুন করে আঁকা |
| **Controls** | মাউস দিয়ে ঘোরানো/জুম (`OrbitControls`) |
| **Loader** | বাইরের মডেল লোড (`GLTFLoader`) |
| **Dispose** | মেমরি পরিষ্কার করা (geometry, material, renderer) |

**অক্ষ:** `x` = ডান/বাম, `y` = উপর/নিচ, `z` = সামনে/পেছনে।
**ঘূর্ণন:** `rotation` রেডিয়ানে (১৮০° = `Math.PI`)।

### 5.2 দুটি উপায়

| উপায় | কখন |
|---|---|
| **Vanilla Three.js** (`useEffect`-এ) | মূল ধারণা শিখতে, বা খুব কাস্টম কিছু |
| **React Three Fiber (R3F)** | React প্রজেক্টে সাধারণত এটাই বেশি সুবিধাজনক |

**পরামর্শ:** প্রথমে ভ্যানিলা উদাহরণ একবার করে বুঝুন, তারপর কাজের জন্য R3F ব্যবহার করুন।

### 5.3 উপায় ১: Vanilla Three.js + React

```jsx
// src/components/three/VanillaCube.jsx
import { useEffect, useRef } from "react";
import * as THREE from "three";

export default function VanillaCube() {
  const mountRef = useRef(null);

  useEffect(() => {
    const mount = mountRef.current;
    const width = mount.clientWidth;
    const height = mount.clientHeight;

    // 1. Scene
    const scene = new THREE.Scene();

    // 2. Camera
    const camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
    camera.position.z = 3;

    // 3. Renderer
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(width, height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    mount.appendChild(renderer.domElement);

    // 4. Mesh
    const geometry = new THREE.BoxGeometry(1, 1, 1);
    const material = new THREE.MeshStandardMaterial({ color: "#4f46e5" });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

    // 5. Light
    scene.add(new THREE.AmbientLight(0xffffff, 0.5));
    const dirLight = new THREE.DirectionalLight(0xffffff, 1.5);
    dirLight.position.set(3, 3, 3);
    scene.add(dirLight);

    // 6. Animation loop
    renderer.setAnimationLoop(() => {
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
    });

    // 7. Resize হ্যান্ডেল
    const onResize = () => {
      const w = mount.clientWidth;
      const h = mount.clientHeight;
      camera.aspect = w / h;
      camera.updateProjectionMatrix();
      renderer.setSize(w, h);
    };
    window.addEventListener("resize", onResize);

    // 8. Cleanup (খুব জরুরি)
    return () => {
      window.removeEventListener("resize", onResize);
      renderer.setAnimationLoop(null);
      geometry.dispose();
      material.dispose();
      renderer.dispose();
      mount.removeChild(renderer.domElement);
    };
  }, []);

  return <div ref={mountRef} style={{ width: "100%", height: "400px" }} />;
}
```

### 5.4 উপায় ২: React Three Fiber (প্রস্তাবিত)

R3F-এ Three.js-এর অবজেক্টগুলো JSX কম্পোনেন্ট হিসেবে লেখা যায়। cleanup, resize, render loop সব নিজে সামলায়।

**নামকরণ নিয়ম:** `THREE.Mesh` → `<mesh>`, `THREE.BoxGeometry` → `<boxGeometry>` (প্রথম অক্ষর ছোট)। কনস্ট্রাক্টর আর্গুমেন্ট যায় `args` প্রপে।

```jsx
// src/components/three/Scene.jsx
import { useRef, useState } from "react";
import { Canvas, useFrame } from "@react-three/fiber";
import { OrbitControls } from "@react-three/drei";

function Box() {
  const meshRef = useRef();
  const [hovered, setHovered] = useState(false);

  // প্রতি ফ্রেমে চলে। delta = আগের ফ্রেম থেকে সময় (সেকেন্ড)
  useFrame((state, delta) => {
    meshRef.current.rotation.y += delta * 0.8;
  });

  return (
    <mesh
      ref={meshRef}
      scale={hovered ? 1.2 : 1}
      onPointerOver={() => setHovered(true)}
      onPointerOut={() => setHovered(false)}
    >
      <boxGeometry args={[1, 1, 1]} />
      <meshStandardMaterial color={hovered ? "#f97316" : "#4f46e5"} />
    </mesh>
  );
}

export default function Scene() {
  return (
    <div style={{ width: "100%", height: "400px" }}>
      <Canvas camera={{ position: [0, 0, 4], fov: 60 }}>
        <ambientLight intensity={0.5} />
        <directionalLight position={[3, 3, 3]} intensity={1.5} />
        <Box />
        <OrbitControls enableZoom={false} />
      </Canvas>
    </div>
  );
}
```

**মনে রাখুন:** `useFrame`-এর ভেতরে `setState` করবেন না (প্রতি ফ্রেমে re-render হয়ে ল্যাগ করবে)। সরাসরি `ref.current` বদলান।

### 5.5 drei: দরকারি হেল্পার

`@react-three/drei` অনেক রেডিমেড জিনিস দেয়:

| হেল্পার | কাজ |
|---|---|
| `OrbitControls` | মাউস দিয়ে ঘোরানো |
| `Environment` | সুন্দর আলো/প্রতিফলন (preset: `"city"`, `"sunset"`) |
| `useGLTF` | GLTF/GLB মডেল লোড |
| `Float` | ভেসে থাকা অ্যানিমেশন |
| `Text` | ৩D টেক্সট |
| `Stars`, `Sparkles` | ব্যাকগ্রাউন্ড ইফেক্ট |
| `ContactShadows` | নিচে নরম ছায়া |
| `Html` | ৩D দৃশ্যের ভেতরে HTML বসানো |

### 5.6 ৩D মডেল লোড (GLB)

`.glb` ফাইল `public/models/shoe.glb`-এ রাখুন।

```jsx
import { Canvas } from "@react-three/fiber";
import { useGLTF, OrbitControls, Environment } from "@react-three/drei";
import { Suspense } from "react";

function Model() {
  const { scene } = useGLTF("/models/shoe.glb");
  return <primitive object={scene} scale={1.5} />;
}

export default function ModelViewer() {
  return (
    <Canvas camera={{ position: [0, 1, 4] }}>
      <Suspense fallback={null}>
        <Model />
        <Environment preset="city" />
      </Suspense>
      <OrbitControls />
    </Canvas>
  );
}
```

> ফ্রি মডেল পাওয়া যায়: Sketchfab, Poly Pizza। মডেল ছোট রাখুন (সম্ভব হলে ৫MB-এর নিচে)।

### 5.7 Three.js: সাধারণ ভুল

- ক্যানভাসের পেরেন্টে `height` না দেওয়া: কিছুই দেখা যাবে না (R3F `Canvas` পেরেন্টের সাইজ নেয়)।
- আলো না দিয়ে `MeshStandardMaterial` ব্যবহার: কালো দেখায়।
- cleanup না করা (ভ্যানিলায়): পেজ বদলালে মেমরি লিক ও একাধিক render loop।
- `useFrame`-এ `setState`: পারফরম্যান্স নষ্ট।
- অনেক বেশি polygon-এর মডেল: মোবাইলে ধীর হয়।
- `devicePixelRatio` সীমা না দেওয়া: হাই-ডিপিআই স্ক্রিনে ভারী হয় (`Math.min(dpr, 2)` দিন)।

---

## 6. Chart.js

### 6.1 থিওরি: মূল ধারণা

প্রতিটি Chart.js চার্টের গঠন একই:

```js
{
  type: "bar",          // চার্টের ধরন
  data: {
    labels: [...],      // X অক্ষের লেবেল
    datasets: [ {...} ] // এক বা একাধিক ডেটাসেট
  },
  options: {...}        // কাস্টমাইজেশন
}
```

| কনসেপ্ট | মানে |
|---|---|
| **type** | `bar`, `line`, `pie`, `doughnut`, `radar`, `polarArea`, `scatter`, `bubble` |
| **labels** | X অক্ষ বা সেগমেন্টের নাম |
| **datasets** | একটা বা একাধিক ডেটা সিরিজ (`label`, `data`, `backgroundColor`, `borderColor`) |
| **scales** | অক্ষ কনফিগ (`x`, `y`: min/max, grid, ticks) |
| **plugins** | legend, title, tooltip ইত্যাদি |
| **responsive** | কন্টেইনারের সাইজ অনুযায়ী বদলানো |
| **maintainAspectRatio** | `false` দিলে কন্টেইনারের height মানে |
| **animation** | চার্ট আঁকার অ্যানিমেশন |

### 6.2 গুরুত্বপূর্ণ: কম্পোনেন্ট রেজিস্টার করতে হবে

Chart.js v3+ **tree-shakable**। যেসব অংশ ব্যবহার করবেন সেগুলো আগে `register` করতে হবে, না হলে `"x" is not a registered scale` ধরনের এরর আসবে।

### 6.3 Bar Chart

```jsx
// src/components/charts/SalesChart.jsx
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend,
} from "chart.js";
import { Bar } from "react-chartjs-2";

ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend);

const options = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { position: "top" },
    title: { display: true, text: "মাসিক বিক্রি" },
  },
  scales: {
    y: { beginAtZero: true },
  },
};

const data = {
  labels: ["জানু", "ফেব্রু", "মার্চ", "এপ্রিল", "মে", "জুন"],
  datasets: [
    {
      label: "বিক্রি (টাকা)",
      data: [12000, 19000, 8000, 15000, 22000, 18000],
      backgroundColor: "rgba(79, 70, 229, 0.7)",
      borderRadius: 6,
    },
  ],
};

export default function SalesChart() {
  return (
    <div style={{ height: "350px" }}>
      <Bar options={options} data={data} />
    </div>
  );
}
```

### 6.4 Line Chart

```jsx
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Filler,
  Tooltip,
  Legend,
} from "chart.js";
import { Line } from "react-chartjs-2";

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Filler, Tooltip, Legend);

const data = {
  labels: ["সোম", "মঙ্গল", "বুধ", "বৃহঃ", "শুক্র"],
  datasets: [
    {
      label: "ভিজিটর",
      data: [120, 190, 150, 220, 300],
      borderColor: "#4f46e5",
      backgroundColor: "rgba(79,70,229,0.15)",
      fill: true,       // লাইনের নিচে রং
      tension: 0.4,     // কার্ভ মসৃণ
      pointRadius: 4,
    },
  ],
};

export default function VisitorsChart() {
  return (
    <div style={{ height: "300px" }}>
      <Line data={data} options={{ responsive: true, maintainAspectRatio: false }} />
    </div>
  );
}
```

### 6.5 Doughnut / Pie Chart

```jsx
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from "chart.js";
import { Doughnut } from "react-chartjs-2";

ChartJS.register(ArcElement, Tooltip, Legend);

const data = {
  labels: ["মোবাইল", "ডেস্কটপ", "ট্যাবলেট"],
  datasets: [
    {
      data: [60, 30, 10],
      backgroundColor: ["#4f46e5", "#f97316", "#10b981"],
      borderWidth: 0,
    },
  ],
};

export default function DeviceChart() {
  return (
    <div style={{ width: "300px" }}>
      <Doughnut data={data} options={{ cutout: "65%" }} />
    </div>
  );
}
```

### 6.6 ডাইনামিক ডেটা আপডেট

`react-chartjs-2`-এ `data` প্রপ বদলালে চার্ট নিজে আপডেট হয়। শুধু নতুন অবজেক্ট দিলেই হয়।

```jsx
import { useMemo, useState } from "react";
import { Bar } from "react-chartjs-2";
// (ChartJS.register আগের মতো)

export default function LiveChart() {
  const [values, setValues] = useState([10, 20, 30, 40]);

  const data = useMemo(
    () => ({
      labels: ["A", "B", "C", "D"],
      datasets: [{ label: "মান", data: values, backgroundColor: "#4f46e5" }],
    }),
    [values]
  );

  const randomize = () =>
    setValues(values.map(() => Math.floor(Math.random() * 100)));

  return (
    <>
      <button onClick={randomize}>ডেটা বদলাও</button>
      <div style={{ height: "300px" }}>
        <Bar data={data} options={{ maintainAspectRatio: false }} />
      </div>
    </>
  );
}
```

### 6.7 কাস্টমাইজেশন চিটশিট

```js
options: {
  responsive: true,
  maintainAspectRatio: false,
  animation: { duration: 1200, easing: "easeOutQuart" },
  plugins: {
    legend: { display: true, position: "bottom" },
    tooltip: {
      callbacks: {
        label: (ctx) => ` ${ctx.dataset.label}: ${ctx.parsed.y} টাকা`,
      },
    },
  },
  scales: {
    x: { grid: { display: false } },
    y: {
      beginAtZero: true,
      grid: { color: "rgba(0,0,0,0.05)" },
      ticks: { callback: (v) => v + " ৳" },
    },
  },
}
```

**ডার্ক মোডে** টেক্সট রং বদলাতে: `ChartJS.defaults.color = "#e5e7eb"`

### 6.8 Chart.js: সাধারণ ভুল

- `register` না করা: "is not a registered scale/element" এরর।
- কন্টেইনারে height না দেওয়া ও `maintainAspectRatio: false` দেওয়া: চার্ট ছোট বা অদৃশ্য হয়।
- প্রতি রেন্ডারে `data`/`options` নতুন করে বানানো: `useMemo` বা কম্পোনেন্টের বাইরে রাখুন।
- একই `<canvas>`-এ দুইবার চার্ট বানানো (ভ্যানিলায়): "Canvas is already in use" এরর। `react-chartjs-2` ব্যবহার করলে এটা হয় না।
- বাংলা লেবেলে ফন্ট না দেখা: ফন্ট সেট করুন `ChartJS.defaults.font.family = "'Noto Sans Bengali', sans-serif"`।

---

## 7. তিনটাকে একসাথে ব্যবহার

### 7.1 মূল নিয়ম

| কে কী নিয়ন্ত্রণ করে | নিয়ম |
|---|---|
| **GSAP → DOM** | `useGSAP` + `ref`/selector |
| **GSAP → Three.js অবজেক্ট** | R3F-এর `ref.current`-এর `position`, `rotation`, `scale`-এ সরাসরি `gsap.to` |
| **Chart.js** | React state/props দিয়ে ডেটা বদলান। এনট্রি অ্যানিমেশন Chart.js-এর নিজের `animation` অপশন দিয়ে হয়। GSAP দিয়ে চার্টের **কন্টেইনার** অ্যানিমেট করুন |

### 7.2 GSAP দিয়ে Three.js অবজেক্ট অ্যানিমেট (R3F)

```jsx
import { useRef } from "react";
import { Canvas } from "@react-three/fiber";
import gsap from "gsap";
import { useGSAP } from "@gsap/react";

function AnimatedBox() {
  const meshRef = useRef();

  useGSAP(() => {
    // rotation ও scale হলো সাধারণ অবজেক্ট, তাই সরাসরি অ্যানিমেট করা যায়
    gsap.to(meshRef.current.rotation, {
      y: Math.PI * 2,
      duration: 3,
      repeat: -1,
      ease: "none",
    });
    gsap.from(meshRef.current.scale, {
      x: 0, y: 0, z: 0,
      duration: 1.2,
      ease: "back.out(1.7)",
    });
  });

  return (
    <mesh ref={meshRef}>
      <boxGeometry />
      <meshStandardMaterial color="#4f46e5" />
    </mesh>
  );
}

export default function Hero3D() {
  return (
    <div style={{ height: "400px" }}>
      <Canvas>
        <ambientLight intensity={0.6} />
        <directionalLight position={[3, 3, 3]} />
        <AnimatedBox />
      </Canvas>
    </div>
  );
}
```

### 7.3 ড্যাশবোর্ড: GSAP + Chart.js

```jsx
import { useRef } from "react";
import gsap from "gsap";
import { useGSAP } from "@gsap/react";
import SalesChart from "./charts/SalesChart";
import DeviceChart from "./charts/DeviceChart";

export default function Dashboard() {
  const container = useRef(null);

  useGSAP(
    () => {
      gsap.from(".panel", {
        opacity: 0,
        y: 40,
        scale: 0.97,
        duration: 0.8,
        stagger: 0.2,
        ease: "power3.out",
      });
    },
    { scope: container }
  );

  return (
    <div ref={container} className="dashboard">
      <div className="panel"><SalesChart /></div>
      <div className="panel"><DeviceChart /></div>
    </div>
  );
}
```

### 7.4 স্ক্রলে ৩D ক্যানভাস দেখানো

```jsx
useGSAP(
  () => {
    gsap.from(".canvas-wrap", {
      opacity: 0,
      y: 100,
      scrollTrigger: { trigger: ".canvas-wrap", start: "top 85%" },
    });
  },
  { scope: container }
);
```

### 7.5 পারফরম্যান্স টিপস

- ৩D ক্যানভাস পেজে **একটা বা দুটোর বেশি** রাখবেন না (প্রতিটা আলাদা WebGL context)।
- যে ক্যানভাস স্ক্রিনের বাইরে, তার render বন্ধ রাখুন (R3F: `<Canvas frameloop="demand">`, আর দরকার হলে `invalidate()`)।
- ভারী কম্পোনেন্ট lazy load করুন:

```jsx
import { lazy, Suspense } from "react";
const Scene = lazy(() => import("./components/three/Scene"));

<Suspense fallback={<p>লোড হচ্ছে...</p>}>
  <Scene />
</Suspense>
```

- অ্যানিমেশনে `x`, `y`, `scale`, `opacity` ব্যবহার করুন।
- `prefers-reduced-motion` সম্মান করুন:

```jsx
useGSAP(() => {
  const mm = gsap.matchMedia();
  mm.add("(prefers-reduced-motion: no-preference)", () => {
    gsap.from(".hero", { opacity: 0, y: 40 });
  });
});
```

---

## 8. চিটশিট ও সাধারণ ভুল

### দ্রুত রেফারেন্স

```jsx
// GSAP
gsap.to(el, { x: 100, duration: 1, ease: "power2.out" });
gsap.from(el, { opacity: 0 });
gsap.timeline().to(a, {...}).to(b, {...}, "-=0.3");
useGSAP(() => {...}, { scope: ref });

// Three.js (R3F)
<Canvas camera={{ position: [0, 0, 5] }}>
  <ambientLight /> <directionalLight position={[3,3,3]} />
  <mesh ref={ref}><boxGeometry /><meshStandardMaterial color="red" /></mesh>
  <OrbitControls />
</Canvas>
useFrame((state, delta) => { ref.current.rotation.y += delta; });

// Chart.js
ChartJS.register(CategoryScale, LinearScale, BarElement, Tooltip, Legend);
<Bar data={data} options={options} />
```

### সমস্যা ও সমাধান

| সমস্যা | সম্ভাব্য কারণ ও সমাধান |
|---|---|
| ৩D ক্যানভাস দেখা যাচ্ছে না | পেরেন্টে `height` নেই, বা আলো নেই |
| ৩D অবজেক্ট কালো | `MeshStandardMaterial`-এ আলো (`ambientLight`) দিন |
| চার্টে "not a registered..." এরর | ব্যবহৃত scale/element `ChartJS.register`-এ যোগ করুন |
| চার্ট কন্টেইনারের সাইজ মানছে না | `maintainAspectRatio: false` + কন্টেইনারে height দিন |
| GSAP অ্যানিমেশন দুইবার চলছে | `useGSAP` ব্যবহার করুন (StrictMode-এর কারণে) |
| ScrollTrigger ভুল জায়গায় চালু হচ্ছে | `ScrollTrigger.refresh()` কল করুন, বা `markers: true` দিয়ে ডিবাগ করুন |
| পেজ ধীর/গরম হচ্ছে | ক্যানভাস কমান, `dpr` সীমিত করুন, মডেল ছোট করুন |
| `window is not defined` (Next.js) | কম্পোনেন্টে `"use client"` দিন, বা `dynamic(() => import(...), { ssr: false })` |

---

## 9. প্র্যাকটিস প্রজেক্ট আইডিয়া

**শুরুর স্তর**
1. GSAP দিয়ে একটা ল্যান্ডিং পেজের hero সেকশনের অ্যানিমেশন
2. Chart.js দিয়ে ৩টা চার্টের (bar, line, doughnut) একটা পেজ

**মাঝারি স্তর**
3. স্ক্রল-ড্রিভেন স্টোরিটেলিং পেজ (GSAP ScrollTrigger + pin)
4. ইন্টারঅ্যাক্টিভ ৩D প্রোডাক্ট ভিউয়ার (R3F + GLB + OrbitControls)

**উন্নত স্তর**
5. অ্যানিমেটেড অ্যানালিটিক্স ড্যাশবোর্ড: GSAP এনট্রি অ্যানিমেশন + Chart.js লাইভ ডেটা + পাশে ৩D হিরো
6. পোর্টফোলিও ওয়েবসাইট: ৩D পার্টিকল ব্যাকগ্রাউন্ড + স্ক্রল অ্যানিমেশন + স্কিল চার্ট

---

## দরকারি লিংক

- GSAP ডকস: https://gsap.com/docs/v3/
- GSAP React গাইড: https://gsap.com/resources/React/
- Three.js ডকস: https://threejs.org/docs/
- Three.js উদাহরণ: https://threejs.org/examples/
- React Three Fiber: https://r3f.docs.pmnd.rs/
- drei: https://drei.docs.pmnd.rs/
- Chart.js ডকস: https://www.chartjs.org/docs/latest/
- react-chartjs-2: https://react-chartjs-2.js.org/

> **টিপ:** একসাথে সব শিখতে যাবেন না। একটা শেষ করে তারপর পরেরটা ধরুন। প্রতিটা অংশের কোড নিজে হাতে টাইপ করে চালান।

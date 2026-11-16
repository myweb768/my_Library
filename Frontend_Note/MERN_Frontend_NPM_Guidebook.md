# 📗 MERN Frontend Guidebook — React আগে, Next.js পরে

> কোন package কী কাজে লাগে, কেন লাগে, কীভাবে ব্যবহার করতে হয় — এক জায়গায়।
> **Part 1:** React (Vite) — এখন যেটা শিখছ
> **Part 2:** Next.js (App Router) — পরে যেটা শিখবে
> **GSAP:** React অংশ (সেকশন 12) ও Next.js অংশ (সেকশন 26) — দুই জায়গায় আলাদা করে
> Backend গাইড (`MERN_Backend_Guidebook.md`) এর সাথে মিলিয়ে পড়ো।

---

## 📑 সূচিপত্র

**PART 1 — React (Vite)**
1. [React vs Next.js: কখন কোনটা](#1-react-vs-next.js:-কখন-কোনটা)
2. [প্রজেক্ট সেটআপ ও ফোল্ডার স্ট্রাকচার](#2-প্রজেক্ট-সেটআপ-ও-ফোল্ডার-স্ট্রাকচার)
3. [Routing: `react-router`](#3-routing-react-router)
4. [Styling: Tailwind, shadcn/ui, clsx, cva](#4-styling)
5. [API call: `axios` + interceptor](#5-api-call-axios)
6. [Server State: `@tanstack/react-query`](#6-server-state-tanstackreact-query)
7. [Client State: `zustand`](#7-client-state-zustand)
8. [Form: `react-hook-form` + `zod`](#8-form-react-hook-form--zod)
9. [Authentication flow (Express backend সহ)](#9-authentication-flow-express-backend-সহ)
10. [UI Utility Packages (toast, icon, modal, table, chart, dnd ...)](#10-ui-utility-packages)
11. [Real-time, File Upload, Payment (frontend দিক)](#11-real-time-file-upload-payment-frontend-দিক)

 🎬 **GSAP — React এ সম্পূর্ণ গাইড**
12. [GSAP — React](#12--gsap--react-এ-সম্পূর্ণ-গাইড)
13. [SEO, i18n, Error Boundary, Performance](#13-seo-i18n-error-boundary-performance)
14. [Testing ও Dev Tools](#14-testing-ও-dev-tools)
15. [Build ও Deploy (React)](#15-build-ও-deploy-react)
16. [React এর Quick Install Cheat Sheet](#16-react-এর-quick-install-cheat-sheet)


**PART 2 — Next.js**

17. [Next.js কী ও কেন, Server vs Client Component ](#17-nextjs-কী-ও-কেন)
18. [সেটআপ ও ফোল্ডার স্ট্রাকচার ](#18-সেটআপ-ও-ফোল্ডার-স্ট্রাকচার)
19. [Routing ও বিশেষ ফাইল ](#19-routing-ও-বিশেষ-ফাইল)
20. [Data Fetching, Caching, Server Actions ](#20-data-fetching-caching-server-actions)
21. [Express Backend এর সাথে Auth ও API](#21-express-backend-এর-সাথে-auth-ও-api)
22. [Next.js এর নিজস্ব Package/Feature](#22-nextjs-এর-নিজস্ব-packagefeature)
23. [React Query, Zustand, Form — Next.js এ](#23-react-query-zustand-form--nextjs-এ)
24. [SEO ও Metadata](#24-seo-ও-metadata)
25. [Rendering Strategy (SSG, ISR, SSR, CSR)](#25-rendering-strategy)


 🎬 **GSAP — Next.js এ সম্পূর্ণ গাইড**

 26. [GSAP — Next.js](#26--gsap--nextjs-এ-সম্পূর্ণ-গাইড)
 27. [Deploy (Next.js)](#27-deploy-nextjs) 
 28. [React → Next.js Migration Checklist](#28-react--nextjs-migration-checklist) 
 29. [Next.js Quick Install](#29-nextjs-quick-install)


**COMMON**

30. [কোন প্রজেক্টে কী লাগে (Decision Table)](#30-কোন-প্রজেক্টে-কী-লাগে-decision-table)
31. [⚠️ Deprecated / এড়িয়ে চলার Package](#31-️-deprecated--এড়িয়ে-চলার-package) 
32. [সাধারণ Error ও সমাধান](#32-সাধারণ-error-ও-সমাধান) 
33. [Security, Accessibility, Best Practices Checklist](#33-security-accessibility-best-practices-checklist) 
34. [📅 Practice Roadmap (দিনে ২-৩টা)](#34--practice-roadmap-দিনে-২-৩টা) 
 
---

# 🟦 PART 1 — React (Vite)

## 1. React vs Next.js: কখন কোনটা

| বিষয় | React (Vite, SPA) | Next.js |
|---|---|---|
| Rendering | Browser এ (CSR) | Server + Browser (SSR, SSG, ISR) |
| SEO | দুর্বল (Google JS চালায়, তবে ধীর/অনিশ্চিত) | চমৎকার |
| Routing | `react-router` (package) | ফাইল-ভিত্তিক, built-in |
| প্রথম লোড | JS bundle নামিয়ে তবে দেখায় | HTML সরাসরি আসে, দ্রুত |
| Backend | আলাদা (Express) | আলাদা Express বা Next এর ভেতরেই API |
| শেখা সহজ | ✅ সহজ | মাঝারি (server/client ধারণা) |
| কখন নেবে | Dashboard, admin panel, login-পরবর্তী app, internal tool | Blog, e-commerce, landing page, marketing site, SEO লাগলে |

**নিয়ম:** Login ছাড়া কেউ দেখে না এমন app (admin/dashboard) → React চলে। Google এ খুঁজে পাওয়া দরকার → Next.js।
React ভালো করে শিখলে Next.js শেখা সহজ, কারণ Next.js আসলে React এর উপরেই বসা।

> **React 19 এর নতুন জিনিস (জেনে রাখো):** `use` hook, `useActionState`, `useOptimistic`, `ref` সরাসরি prop হিসেবে (`forwardRef` আর লাগে না), component এর ভেতরেই `<title>`/`<meta>` লেখা যায়। React Compiler (ঐচ্ছিক) থাকলে `useMemo`/`useCallback` হাতে লেখার দরকার কমে।

---

## 2. প্রজেক্ট সেটআপ ও ফোল্ডার স্ট্রাকচার

> ⚠️ `create-react-app` **deprecated**। এখন Vite ব্যবহার করো।

```bash
npm create vite@latest my-app -- --template react      # JavaScript
# npm create vite@latest my-app -- --template react-ts  # TypeScript
cd my-app
npm install
npm run dev
```

> এই গাইডের উদাহরণ **JavaScript (.jsx)**। TypeScript শিখলে `.jsx → .tsx` করে type যোগ করলেই চলবে।

### প্রস্তাবিত ফোল্ডার স্ট্রাকচার

```
my-app/
├── public/                 # favicon, static file
├── src/
│   ├── assets/             # image, font
│   ├── components/
│   │   ├── ui/             # Button, Input, Modal (পুনর্ব্যবহারযোগ্য)
│   │   └── layout/         # Navbar, Footer, Sidebar
│   ├── features/           # feature অনুযায়ী ভাগ (বড় প্রজেক্টে)
│   │   ├── auth/           # LoginForm, useAuth, auth.api.js
│   │   └── products/       # ProductCard, useProducts, products.api.js
│   ├── pages/              # Home, Login, Dashboard ...
│   ├── hooks/              # useDebounce, useLocalStorage ...
│   ├── lib/                # axios.js, queryClient.js, utils.js
│   ├── store/              # zustand store
│   ├── router.jsx
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── .env
├── .env.example
├── jsconfig.json
├── vite.config.js
└── package.json
```

### Path alias (`@/` দিয়ে import)

```js
// vite.config.js
import path from "path";
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: { alias: { "@": path.resolve(__dirname, "./src") } },
  server: {
    // Dev এ backend এর সাথে CORS সমস্যা এড়াতে proxy
    proxy: { "/api": { target: "http://localhost:5000", changeOrigin: true } },
  },
});
```

```json
// jsconfig.json  (VS Code এ autocomplete এর জন্য)
{ "compilerOptions": { "baseUrl": ".", "paths": { "@/*": ["./src/*"] } } }
```

```js
import Button from "@/components/ui/Button";   // ../../../components/ui/Button এর বদলে
```

### Environment Variable

```
# .env
VITE_API_URL=http://localhost:5000/api/v1
```

```js
const url = import.meta.env.VITE_API_URL;
```

> ⚠️ শুধু `VITE_` দিয়ে শুরু হওয়া variable ই browser এ যায়, আর সেগুলো **সবাই দেখতে পায়**। Secret key (Stripe secret, JWT secret) কখনো frontend এ রাখবে না।

---

## 3. Routing: `react-router`
[**Reacr-Router Doc**](https://reactrouter.com/home)
- **কাজ:** SPA তে পেজ বদল (URL বদলায়, কিন্তু পেজ reload হয় না)।
- **নোট:** v7 এ `react-router-dom` এর কাজ `react-router` package এই একত্র হয়েছে। পুরনো tutorial এ `react-router-dom` দেখলে ভয় পেও না, কনসেপ্ট একই। Install এর সময় official docs দেখে import path মিলিয়ে নিও।

```bash
npm i react-router
```

### Router তৈরি

```jsx
// src/router.jsx
import { createBrowserRouter } from "react-router";
import RootLayout from "@/components/layout/RootLayout";
import ProtectedRoute from "@/components/ProtectedRoute";
import Home from "@/pages/Home";
import Login from "@/pages/Login";
import Products from "@/pages/Products";
import ProductDetails from "@/pages/ProductDetails";
import NotFound from "@/pages/NotFound";
import ErrorPage from "@/pages/ErrorPage";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,          // Navbar + <Outlet /> + Footer
    errorElement: <ErrorPage />,
    children: [
      { index: true, element: <Home /> },
      { path: "products", element: <Products /> },
      { path: "products/:id", element: <ProductDetails /> },
      { path: "login", element: <Login /> },

      // Login লাগবে এমন route গুলো
      {
        element: <ProtectedRoute />,
        children: [
          {
            path: "dashboard",
            // Lazy loading: এই পেজ দরকার হলে তবেই JS নামবে (bundle ছোট হয়)
            lazy: async () => {
              const { default: Component } = await import("@/pages/Dashboard");
              return { Component };
            },
          },
        ],
      },
      { path: "*", element: <NotFound /> },
    ],
  },
]);
```

```jsx
// src/main.jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { RouterProvider } from "react-router/dom";
import { router } from "./router";
import "./index.css";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>
);
```

```jsx
// RootLayout.jsx
import { Outlet } from "react-router";

export default function RootLayout() {
  return (
    <>
      <Navbar />
      <main><Outlet /></main>   {/* child route এখানে বসে */}
      <Footer />
    </>
  );
}
```

### গুরুত্বপূর্ণ Hook ও Component

```jsx
import { Link, NavLink, Navigate, useNavigate, useParams, useSearchParams, useLocation } from "react-router";

// Link: পেজ reload ছাড়া যাওয়া (<a href> এর বদলে)
<Link to="/products">Products</Link>

// NavLink: active থাকলে স্টাইল দেওয়া যায়
<NavLink to="/dashboard" className={({ isActive }) => (isActive ? "text-blue-600 font-bold" : "text-gray-600")}>
  Dashboard
</NavLink>

// useParams: URL থেকে /products/:id এর id
const { id } = useParams();

// useSearchParams: ?page=2&sort=price
const [searchParams, setSearchParams] = useSearchParams();
const page = Number(searchParams.get("page")) || 1;
setSearchParams({ page: page + 1 });

// useNavigate: কোড থেকে redirect
const navigate = useNavigate();
navigate("/dashboard", { replace: true });   // replace: back চাপলে login এ ফিরবে না
navigate(-1);                                // পেছনে যাও

// useLocation: বর্তমান path/state
const location = useLocation();
```

### Protected Route

```jsx
// src/components/ProtectedRoute.jsx
import { Navigate, Outlet, useLocation } from "react-router";
import { useMe } from "@/features/auth/useAuth";

export default function ProtectedRoute({ roles }) {
  const { data: user, isPending } = useMe();
  const location = useLocation();

  if (isPending) return <FullPageSpinner />;

  if (!user) {
    // login এর পর আবার এই পেজেই ফেরানোর জন্য from সেভ রাখা
    return <Navigate to="/login" replace state={{ from: location }} />;
  }
  if (roles && !roles.includes(user.role)) return <Navigate to="/403" replace />;

  return <Outlet />;
}

// ব্যবহার: { element: <ProtectedRoute roles={["admin"]} />, children: [...] }
```

> Frontend এর route protection শুধু UX এর জন্য। **আসল নিরাপত্তা backend এ**, যেখানে প্রতিটি API তে token/role check হয়।

---

## 4. Styling

### Tailwind CSS (v4, Vite সহ)
[**Tailwind Doc**](https://tailwindcss.com/docs/installation/using-vite)

```bash
npm i tailwindcss @tailwindcss/vite
```

```css
/* src/index.css */
@import "tailwindcss";
```

(`vite.config.js` এ `tailwindcss()` plugin যোগ করা সেকশন 2 এ দেখানো আছে।)

```jsx
<button className="rounded-lg bg-blue-600 px-4 py-2 text-white hover:bg-blue-700 md:px-6">
  Click
</button>
```

- Responsive: `sm:` `md:` `lg:` prefix (mobile-first)
- Dark mode: `dark:bg-gray-900`
- Hover/focus: `hover:` `focus:`

### সহায়ক package

```bash
npm i clsx tailwind-merge class-variance-authority lucide-react
```

```js
// src/lib/utils.js — shadcn/ui সহ প্রায় সবাই এই helper রাখে
import { clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export const cn = (...inputs) => twMerge(clsx(inputs));
// cn("px-2 py-1", isActive && "bg-blue-500", "px-4")  → "py-1 bg-blue-500 px-4"  (px-2 বাদ, কারণ conflict)
```

```jsx
// Variant সহ Button (class-variance-authority)
import { cva } from "class-variance-authority";
import { cn } from "@/lib/utils";

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md font-medium transition disabled:opacity-50",
  {
    variants: {
      variant: {
        primary: "bg-blue-600 text-white hover:bg-blue-700",
        outline: "border border-gray-300 hover:bg-gray-100",
        danger: "bg-red-600 text-white hover:bg-red-700",
      },
      size: { sm: "h-8 px-3 text-sm", md: "h-10 px-4", lg: "h-12 px-6 text-lg" },
    },
    defaultVariants: { variant: "primary", size: "md" },
  }
);

export default function Button({ variant, size, className, ...props }) {
  return <button className={cn(buttonVariants({ variant, size }), className)} {...props} />;
}
// <Button variant="danger" size="sm">Delete</Button>
```

### shadcn/ui (জনপ্রিয় component collection)
[**Shadcn Doc**](https://ui.shadcn.com/docs)

```bash
npx shadcn@latest init
npx shadcn@latest add button input dialog dropdown-menu table sonner
```

- এটা package না, কোড তোমার প্রজেক্টে **কপি হয়ে আসে** (`components/ui/`), তাই যেভাবে খুশি বদলাতে পারো।
- ভেতরে Radix UI (accessible) + Tailwind।

### Dark Mode
[**Next-Theme Doc**](https://github.com/pacocoursey/next-themes)

```bash
npm i next-themes      # নামে next থাকলেও সাধারণ React এও চলে
```

```jsx
import { ThemeProvider, useTheme } from "next-themes";

<ThemeProvider attribute="class" defaultTheme="system" enableSystem>
  <App />
</ThemeProvider>

const { theme, setTheme } = useTheme();
<button onClick={() => setTheme(theme === "dark" ? "light" : "dark")}>Toggle</button>
```

### অন্যান্য UI Library (বিকল্প)

| Library | বৈশিষ্ট্য | Doc |
|---|---|---|
| `@mui/material` | Google Material, বড় ও সম্পূর্ণ | [Doc](https://mui.com/material-ui/getting-started/) |
| `@mantine/core` | অনেক component ও hook, সুন্দর | [Doc](https://mantine.dev/getting-started/) |
| `@chakra-ui/react` | সহজ API | [Doc](https://chakra-ui.com/docs/get-started/installation) |
| `heroui` | Tailwind-ভিত্তিক, সুন্দর | [Doc](https://heroui.com/en/docs/react/getting-started) |
| `@headlessui/react` | স্টাইলহীন accessible component | [Doc](https://github.com/tailwindlabs/headlessui) |
| `daisyui` | Tailwind plugin, তৈরি-করা class | [Doc](https://daisyui.com/docs/intro/) |

---

## 5. API Call: `axios`

[**Axios Doc**](https://axios.rest/pages/getting-started/first-steps.html)

```bash
npm i axios
```

```js
// src/lib/axios.js
import axios from "axios";

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  withCredentials: true,        // httpOnly cookie পাঠানো/নেওয়ার জন্য (backend এ cors credentials: true লাগবে)
  timeout: 15000,
});

// ── Response interceptor: ৪০১ হলে একবার refresh করে আবার চেষ্টা ──
let refreshPromise = null;

api.interceptors.response.use(
  (res) => res,
  async (error) => {
    const original = error.config;
    const isAuthCall = original?.url?.includes("/auth/");

    if (error.response?.status === 401 && !original._retry && !isAuthCall) {
      original._retry = true;
      try {
        // একসাথে অনেক request ৪০১ পেলে refresh একবারই হবে
        refreshPromise ??= api.post("/auth/refresh").finally(() => (refreshPromise = null));
        await refreshPromise;
        return api(original);             // পুরনো request আবার
      } catch {
        window.location.href = "/login";  // refresh ও ব্যর্থ
      }
    }
    return Promise.reject(error);
  }
);

// Error থেকে backend এর message বের করার helper
export const getErrorMessage = (error) =>
  error.response?.data?.message || error.message || "কিছু একটা ভুল হয়েছে";
```

```js
// ব্যবহার
const { data } = await api.get("/products", { params: { page: 2, limit: 10 } });
await api.post("/products", { name: "Shoe", price: 500 });
await api.patch(`/products/${id}`, { price: 600 });
await api.delete(`/products/${id}`);
```

> Axios এর বদলে `fetch` ও চলে, কিন্তু interceptor, timeout, auto JSON এর জন্য axios সুবিধাজনক। Token localStorage এ না রেখে **httpOnly cookie** তে রাখা নিরাপদ (XSS থেকে সুরক্ষা)।

---

## 6. Server State: `@tanstack/react-query`

[**Tanstack/React-Quert Doc**](https://tanstack.com/query/latest/docs/framework/react/overview)

**কেন?** API থেকে আনা data এর জন্য loading/error state, cache, refetch, pagination হাতে `useEffect + useState` দিয়ে লেখা কষ্টের ও ভুলপ্রবণ। React Query এগুলো নিজে করে।

```bash
npm i @tanstack/react-query
npm i -D @tanstack/react-query-devtools
```

### Setup

```jsx
// src/lib/queryClient.js
import { QueryClient } from "@tanstack/react-query";

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 60 * 1000,          // ১ মিনিট data "fresh" ধরা হবে, এর মধ্যে আবার fetch করবে না
      retry: 1,
      refetchOnWindowFocus: false,
    },
  },
});
```

```jsx
// main.jsx
import { QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

<QueryClientProvider client={queryClient}>
  <RouterProvider router={router} />
  <ReactQueryDevtools initialIsOpen={false} />
</QueryClientProvider>
```

### পড়া: `useQuery`

```jsx
// src/features/products/useProducts.js
import { useQuery, keepPreviousData } from "@tanstack/react-query";
import { api } from "@/lib/axios";

export const useProducts = ({ page = 1, search = "" } = {}) =>
  useQuery({
    queryKey: ["products", { page, search }],     // key বদলালে অটো নতুন করে fetch
    queryFn: async () => (await api.get("/products", { params: { page, search } })).data,
    placeholderData: keepPreviousData,            // পেজ বদলের সময় আগের data দেখায়, ঝিলিক লাগে না
  });

export const useProduct = (id) =>
  useQuery({
    queryKey: ["products", id],
    queryFn: async () => (await api.get(`/products/${id}`)).data.data,
    enabled: !!id,                                // id না থাকলে চালাবে না
  });
```

```jsx
function Products() {
  const [page, setPage] = useState(1);
  const { data, isPending, isError, error, isFetching } = useProducts({ page });

  if (isPending) return <Skeleton />;
  if (isError) return <p>{getErrorMessage(error)}</p>;

  return (
    <>
      {data.data.map((p) => <ProductCard key={p._id} product={p} />)}
      <button disabled={page === 1} onClick={() => setPage((p) => p - 1)}>Prev</button>
      <button disabled={page >= data.totalPages} onClick={() => setPage((p) => p + 1)}>Next</button>
      {isFetching && <span>Updating...</span>}
    </>
  );
}
```

### লেখা: `useMutation`

[**Sonner Doc**](https://sonner.emilkowal.ski/getting-started)

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";
import { toast } from "sonner";

export const useCreateProduct = () => {
  const qc = useQueryClient();
  return useMutation({
    mutationFn: (body) => api.post("/products", body).then((r) => r.data),
    onSuccess: () => {
      toast.success("Product তৈরি হয়েছে");
      qc.invalidateQueries({ queryKey: ["products"] });   // list পুরনো, আবার আনো
    },
    onError: (err) => toast.error(getErrorMessage(err)),
  });
};

// ব্যবহার
const { mutate, isPending } = useCreateProduct();
<button disabled={isPending} onClick={() => mutate({ name: "Shoe", price: 500 })}>Add</button>
```

### Optimistic Update (UI আগে বদলায়, server পরে)

```jsx
useMutation({
  mutationFn: (id) => api.delete(`/products/${id}`),
  onMutate: async (id) => {
    await qc.cancelQueries({ queryKey: ["products"] });
    const previous = qc.getQueriesData({ queryKey: ["products"] });
    qc.setQueriesData({ queryKey: ["products"] }, (old) =>
      old ? { ...old, data: old.data.filter((p) => p._id !== id) } : old
    );
    return { previous };
  },
  onError: (_e, _id, ctx) => ctx.previous.forEach(([key, data]) => qc.setQueryData(key, data)), // ভুল হলে ফেরত
  onSettled: () => qc.invalidateQueries({ queryKey: ["products"] }),
});
```

### Infinite Scroll

```jsx
const { data, fetchNextPage, hasNextPage, isFetchingNextPage } = useInfiniteQuery({
  queryKey: ["feed"],
  queryFn: ({ pageParam }) => api.get("/feed", { params: { page: pageParam } }).then((r) => r.data),
  initialPageParam: 1,
  getNextPageParam: (last) => (last.page < last.totalPages ? last.page + 1 : undefined),
});
const items = data?.pages.flatMap((p) => p.data) ?? [];
```

**Query key এর নিয়ম:** `["products"]` (list), `["products", id]` (single), `["products", { page, search }]` (filter সহ)। `invalidateQueries({ queryKey: ["products"] })` দিলে এই prefix এর সবকটা refresh হয়।

বিকল্প: `swr` (হালকা)।

---

## 7. Client State: `zustand`

[**Zustand Doc**](https://github.com/pmndrs/zustand)

**কখন লাগে:** Server থেকে আসা না, শুধু UI এর state — cart, sidebar খোলা/বন্ধ, filter, modal।

```bash
npm i zustand
```

```js
// src/store/cart.store.js
import { create } from "zustand";
import { persist } from "zustand/middleware";

export const useCart = create(
  persist(
    (set, get) => ({
      items: [],
      add: (product) =>
        set((state) => {
          const found = state.items.find((i) => i._id === product._id);
          return found
            ? { items: state.items.map((i) => (i._id === product._id ? { ...i, qty: i.qty + 1 } : i)) }
            : { items: [...state.items, { ...product, qty: 1 }] };
        }),
      remove: (id) => set((s) => ({ items: s.items.filter((i) => i._id !== id) })),
      clear: () => set({ items: [] }),
      total: () => get().items.reduce((sum, i) => sum + i.price * i.qty, 0),
    }),
    { name: "cart-storage" }     // localStorage এ সেভ থাকবে, refresh করলেও যাবে না
  )
);
```

```jsx
// ⚠️ selector দিয়ে শুধু যেটা দরকার সেটা নাও (নাহলে অকারণে re-render)
const items = useCart((s) => s.items);
const add = useCart((s) => s.add);
const count = useCart((s) => s.items.reduce((n, i) => n + i.qty, 0));
```

**নিয়ম:** Server data → React Query। UI/local state → Zustand। একই data দুই জায়গায় রেখো না।
বিকল্প: `@reduxjs/toolkit` + `react-redux` (বড় টিম/বড় প্রজেক্টে), `jotai` (ছোট atom)।

---

## 8. Form: `react-hook-form` + `zod`

[**React-Hook-Form Doc**](https://react-hook-form.com/get-started)| |[**Zod Doc**](https://zod.dev/)

```bash
npm i react-hook-form zod @hookform/resolvers
```

- `react-hook-form`: ফর্ম state ও submit (দ্রুত, কম re-render)
- `zod`: validation rule (backend এও একই schema এর ধারণা)
- `@hookform/resolvers`: দুটোকে জোড়া লাগায়

### Login ফর্ম

```jsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

const loginSchema = z.object({
  email: z.string().min(1, "Email দাও").email("সঠিক email দাও"),
  password: z.string().min(6, "কমপক্ষে ৬ অক্ষর"),
});

export default function LoginForm() {
  const login = useLogin();    // useMutation hook

  const {
    register,
    handleSubmit,
    setError,
    formState: { errors, isSubmitting },
  } = useForm({
    resolver: zodResolver(loginSchema),
    defaultValues: { email: "", password: "" },
  });

  const onSubmit = async (values) => {
    try {
      await login.mutateAsync(values);
    } catch (err) {
      // backend এর error ফর্মে দেখানো
      setError("root", { message: getErrorMessage(err) });
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} noValidate>
      <input type="email" placeholder="Email" {...register("email")} />
      {errors.email && <p className="text-red-500">{errors.email.message}</p>}

      <input type="password" placeholder="Password" {...register("password")} />
      {errors.password && <p className="text-red-500">{errors.password.message}</p>}

      {errors.root && <p className="text-red-500">{errors.root.message}</p>}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Loading..." : "Login"}
      </button>
    </form>
  );
}
```

### আরও দরকারি ফিচার

```jsx
// ১) পাসওয়ার্ড মিলানো (refine)
const registerSchema = z
  .object({
    name: z.string().min(2),
    email: z.string().email(),
    password: z.string().min(6),
    confirmPassword: z.string(),
  })
  .refine((d) => d.password === d.confirmPassword, {
    message: "Password মিলছে না",
    path: ["confirmPassword"],
  });

// ২) সংখ্যা (input সবসময় string দেয়, তাই coerce)
const productSchema = z.object({
  name: z.string().min(1),
  price: z.coerce.number().positive("দাম ০ এর বেশি হতে হবে"),
});

// ৩) Controller: বাইরের UI component (shadcn Select, DatePicker) এর জন্য
import { Controller } from "react-hook-form";
<Controller
  name="category"
  control={control}
  render={({ field }) => <Select value={field.value} onValueChange={field.onChange}>...</Select>}
/>

// ৪) useFieldArray: ডাইনামিক list (একাধিক item, skill ইত্যাদি)
const { fields, append, remove } = useFieldArray({ control, name: "items" });

// ৫) edit ফর্মে server data দিয়ে ভরা
useEffect(() => { if (product) reset(product); }, [product, reset]);

// ৬) অন্য field দেখে কিছু করা
const price = watch("price");
```

> Zod এর নতুন version এ `z.email()` ও লেখা যায় (`z.string().email()` ও চলে)। Install এর পর docs মিলিয়ে নিও।
> বিকল্প: `formik` + `yup` (পুরনো ধারা), `@tanstack/react-form`।

---

## 9. Authentication Flow (Express Backend সহ)

**পরিকল্পনা:** Backend httpOnly cookie তে token দেয় → Frontend token নিজে ছোঁয় না → `/auth/me` দিয়ে "আমি কে" জানে।

```js
// src/features/auth/auth.api.js
import { api } from "@/lib/axios";

export const authApi = {
  login: (body) => api.post("/auth/login", body).then((r) => r.data),
  register: (body) => api.post("/auth/register", body).then((r) => r.data),
  logout: () => api.post("/auth/logout"),
  me: () => api.get("/auth/me").then((r) => r.data.data),
};
```

```jsx
// src/features/auth/useAuth.js
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";
import { useNavigate, useLocation } from "react-router";
import { authApi } from "./auth.api";

// বর্তমান user (cookie থাকলে আসে, না থাকলে null)
export const useMe = () =>
  useQuery({
    queryKey: ["me"],
    queryFn: () => authApi.me().catch((e) => (e.response?.status === 401 ? null : Promise.reject(e))),
    staleTime: 5 * 60 * 1000,
    retry: false,
  });

export const useLogin = () => {
  const qc = useQueryClient();
  const navigate = useNavigate();
  const location = useLocation();

  return useMutation({
    mutationFn: authApi.login,
    onSuccess: async () => {
      await qc.invalidateQueries({ queryKey: ["me"] });
      navigate(location.state?.from?.pathname || "/dashboard", { replace: true });
    },
  });
};

export const useLogout = () => {
  const qc = useQueryClient();
  const navigate = useNavigate();
  return useMutation({
    mutationFn: authApi.logout,
    onSuccess: () => {
      qc.clear();                          // সব cache মুছো (অন্য user এর data যেন না থাকে)
      navigate("/login", { replace: true });
    },
  });
};
```

**Backend এ যা লাগবে** (Backend গাইডের সাথে মিলিয়ে):
- `cors({ origin: CLIENT_URL, credentials: true })`
- Cookie: `httpOnly`, `secure` (production), `sameSite`
- `GET /auth/me`, `POST /auth/refresh`, `POST /auth/logout`

**Frontend ও Backend আলাদা domain হলে** (যেমন `app.com` ও `api.com`): cookie `sameSite: "none"` + `secure: true` লাগে, নাহলে browser cookie আটকে দেয়। সবচেয়ে সহজ সমাধান হলো একই domain এর নিচে রাখা (`app.com` ও `app.com/api`), অথবা dev এ Vite proxy।

**Token localStorage এ রাখলে?** কাজ করে, কিন্তু XSS হলে চুরি যায়। তাই cookie ভালো।

---

## 10. UI Utility Packages

| Package | কাজ | উদাহরণ |
|---|---|---|
| `sonner` | Toast notification | `toast.success("Saved")` + `<Toaster />` একবার root এ |
| `lucide-react` | Icon | `<Trash2 size={16} />` |
| `react-icons` | অনেক icon set একসাথে | `import { FaFacebook } from "react-icons/fa"` |
| `@radix-ui/react-*` | Dialog, Dropdown, Tabs (accessible) | shadcn এর ভেতরে এটাই |
| `vaul` | Mobile drawer/bottom sheet | |
| `cmdk` | Command palette (Ctrl+K) | |
| `react-day-picker` | Date picker | |
| `react-select` | Searchable select | |
| `react-loading-skeleton` | Loading skeleton | |
| `react-hot-toast` | Sonner এর বিকল্প | |
| `swiper` / `embla-carousel-react` | Carousel/slider | |
| `yet-another-react-lightbox` | Image gallery | |
| `react-markdown` | Markdown দেখানো | |
| `tiptap` (`@tiptap/react`) | Rich text editor | |
| `react-to-print` / `@react-pdf/renderer` | Print/PDF | |
| `react-qr-code` | QR code | |

### Data Table (`@tanstack/react-table`)

```bash
npm i @tanstack/react-table
```

```jsx
import { useReactTable, getCoreRowModel, getSortedRowModel, flexRender } from "@tanstack/react-table";

const columns = [
  { accessorKey: "name", header: "Name" },
  { accessorKey: "price", header: "Price", cell: (info) => `৳${info.getValue()}` },
  { id: "actions", header: "", cell: ({ row }) => <button onClick={() => onDelete(row.original._id)}>Delete</button> },
];

function DataTable({ data }) {
  const table = useReactTable({ data, columns, getCoreRowModel: getCoreRowModel(), getSortedRowModel: getSortedRowModel() });

  return (
    <table>
      <thead>
        {table.getHeaderGroups().map((hg) => (
          <tr key={hg.id}>
            {hg.headers.map((h) => (
              <th key={h.id} onClick={h.column.getToggleSortingHandler()}>
                {flexRender(h.column.columnDef.header, h.getContext())}
              </th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody>
        {table.getRowModel().rows.map((row) => (
          <tr key={row.id}>
            {row.getVisibleCells().map((c) => (
              <td key={c.id}>{flexRender(c.column.columnDef.cell, c.getContext())}</td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

> বড় data (হাজার row) হলে server-side pagination/sort করো (backend এ), আর দরকারে `@tanstack/react-virtual`।

### Chart (`recharts`)

```jsx
import { LineChart, Line, XAxis, YAxis, Tooltip, CartesianGrid, ResponsiveContainer } from "recharts";

<ResponsiveContainer width="100%" height={300}>
  <LineChart data={[{ month: "Jan", sales: 400 }, { month: "Feb", sales: 700 }]}>
    <CartesianGrid strokeDasharray="3 3" />
    <XAxis dataKey="month" />
    <YAxis />
    <Tooltip />
    <Line type="monotone" dataKey="sales" stroke="#2563eb" />
  </LineChart>
</ResponsiveContainer>
```

বিকল্প: `chart.js` + `react-chartjs-2`, `@nivo/*`, `visx`।

### Drag & Drop (`@dnd-kit`)

```bash
npm i @dnd-kit/core @dnd-kit/sortable @dnd-kit/utilities
```
Kanban board, sortable list এর জন্য। (`react-beautiful-dnd` deprecated, ব্যবহার করো না।)

### Debounce ও Utility Hook

```bash
npm i use-debounce usehooks-ts react-intersection-observer dayjs
```

```jsx
import { useDebounce } from "use-debounce";

const [search, setSearch] = useState("");
const [debounced] = useDebounce(search, 500);          // টাইপ থামার ৫০০ms পর
const { data } = useProducts({ search: debounced });   // প্রতি key চাপে API call হবে না
```

```jsx
import { useInView } from "react-intersection-observer";
const { ref, inView } = useInView();          // স্ক্রলে দেখা গেলে infinite scroll এ fetchNextPage
useEffect(() => { if (inView && hasNextPage) fetchNextPage(); }, [inView]);
```

`usehooks-ts` এ আছে `useLocalStorage`, `useMediaQuery`, `useOnClickOutside`, `useCopyToClipboard` ইত্যাদি তৈরি hook।


---

## 11. Real-time, File Upload, Payment (Frontend দিক)

### 11.1 Socket.io Client

```bash
npm i socket.io-client
```

```js
// src/lib/socket.js
import { io } from "socket.io-client";

export const socket = io(import.meta.env.VITE_SOCKET_URL, {
  withCredentials: true,
  autoConnect: false,        // login এর পর নিজে connect করব
});
```

```jsx
// src/hooks/useSocketEvent.js
import { useEffect } from "react";
import { socket } from "@/lib/socket";

export function useSocketEvent(event, handler) {
  useEffect(() => {
    socket.on(event, handler);
    return () => socket.off(event, handler);   // cleanup না করলে একই event বারবার চলবে
  }, [event, handler]);
}

// ব্যবহার (chat)
const [messages, setMessages] = useState([]);
const onMessage = useCallback((msg) => setMessages((prev) => [...prev, msg]), []);
useSocketEvent("message", onMessage);

socket.emit("message", { to: userId, text: "Hi" });
```

```jsx
// App এর উপরে একবার: user থাকলে connect, logout এ disconnect
const { data: user } = useMe();
useEffect(() => {
  if (user) socket.connect();
  return () => socket.disconnect();
}, [user]);
```

Socket এর data কে React Query cache এও আপডেট করা যায়: `queryClient.setQueryData(["messages"], (old) => [...old, msg])`।

### 11.2 File Upload (`react-dropzone`)

```bash
npm i react-dropzone
```

```jsx
import { useDropzone } from "react-dropzone";
import { useState } from "react";

export default function ImageUpload({ onUploaded }) {
  const [preview, setPreview] = useState(null);
  const [progress, setProgress] = useState(0);

  const { getRootProps, getInputProps, isDragActive } = useDropzone({
    accept: { "image/*": [] },
    maxSize: 5 * 1024 * 1024,         // 5MB
    multiple: false,
    onDropRejected: () => toast.error("শুধু ৫MB এর ছোট ছবি দাও"),
    onDrop: async ([file]) => {
      if (!file) return;
      setPreview(URL.createObjectURL(file));       // সঙ্গে সঙ্গে preview

      const form = new FormData();
      form.append("avatar", file);                 // backend এর multer field নামের সাথে মিলতে হবে

      const { data } = await api.post("/users/avatar", form, {
        headers: { "Content-Type": "multipart/form-data" },
        onUploadProgress: (e) => setProgress(Math.round((e.loaded * 100) / e.total)),
      });
      onUploaded?.(data.url);
    },
  });

  return (
    <div {...getRootProps()} className="cursor-pointer rounded-lg border-2 border-dashed p-8 text-center">
      <input {...getInputProps()} />
      {preview ? <img src={preview} alt="preview" className="mx-auto h-32" /> : (
        <p>{isDragActive ? "ছেড়ে দাও..." : "ছবি টেনে আনো বা ক্লিক করো"}</p>
      )}
      {progress > 0 && progress < 100 && <p>{progress}%</p>}
    </div>
  );
}
```

ছবি compress করতে: `browser-image-compression`। ছবি কাটতে: `react-easy-crop`।

### 11.3 Payment

**SSLCommerz (backend গাইড 10.1):**
```jsx
const pay = useMutation({
  mutationFn: (body) => api.post("/payment/init", body).then((r) => r.data),
  onSuccess: (data) => { window.location.href = data.url; },   // SSLCommerz এর পেজে নিয়ে যায়
});
```

```jsx
// /payment/success?tran_id=...  পেজ — শুধু মেসেজ দেখাও
const [params] = useSearchParams();
const tranId = params.get("tran_id");
// আসল status backend থেকে নিয়ে দেখাও: GET /orders/by-tran/:tranId
```

**Stripe:**
```bash
npm i @stripe/stripe-js @stripe/react-stripe-js
```
সবচেয়ে সহজ উপায়: backend Checkout Session বানিয়ে `url` দেয়, frontend শুধু `window.location.href = url` করে। Card ফর্ম নিজের পেজে চাইলে `<Elements>` + `<PaymentElement>` ব্যবহার করো।

---

## 12. 🎬 GSAP — React এ সম্পূর্ণ গাইড

### 12.1 GSAP কী ও কখন লাগে

GSAP (GreenSock) হলো সবচেয়ে শক্তিশালী JavaScript animation library। জটিল timeline, scroll-driven animation, text animation, SVG, পেজ ট্রানজিশন — এসবে সেরা।

| কাজ | কী ব্যবহার করবে |
|---|---|
| সাধারণ hover/fade/transition | **CSS** বা Tailwind (`transition`, `animate-*`) |
| Component enter/exit, layout animation | **`motion`** (আগের framer-motion) |
| Scroll animation, timeline, জটিল sequence, landing page, portfolio | **GSAP** |
| 3D | `three` + `@react-three/fiber` |
| Lottie file | `lottie-react` |

> **GSAP এখন সম্পূর্ণ ফ্রি** (Webflow অধিগ্রহণের পর)। ScrollTrigger, SplitText, Flip, MorphSVG ইত্যাদি সব plugin এখন মূল `gsap` package এর ভেতরেই আছে, আলাদা পেইড সাবস্ক্রিপশন বা আলাদা install লাগে না। (তবুও install এর সময় gsap.com এ license/নোট একবার দেখে নিও।)

### 12.2 Install

```bash
npm i gsap @gsap/react
```

- `gsap`: মূল library ও সব plugin
- `@gsap/react`: **`useGSAP` hook**। React এ GSAP চালানোর অফিসিয়াল ও সঠিক উপায় (cleanup নিজে করে)

```bash
npm i lenis       # smooth scroll (ঐচ্ছিক, কিন্তু জনপ্রিয়)
```

### 12.3 মূল ধারণা: Tween ও Timeline

```js
gsap.to(".box", { x: 200, duration: 1 });              // এখনকার অবস্থা → x:200
gsap.from(".box", { opacity: 0, y: 50, duration: 1 }); // opacity:0,y:50 → এখনকার অবস্থা
gsap.fromTo(".box", { opacity: 0 }, { opacity: 1, duration: 1 }); // শুরু ও শেষ দুটোই নিজে ঠিক করো
gsap.set(".box", { opacity: 0 });                      // সঙ্গে সঙ্গে বসিয়ে দাও (animation ছাড়া)
```

**সাধারণ property:**

| Property | মানে |
|---|---|
| `x`, `y` | সরানো (px, transform দিয়ে — দ্রুত) |
| `xPercent`, `yPercent` | নিজের মাপের % এ সরানো |
| `scale`, `rotation` | বড়-ছোট, ঘোরানো |
| `opacity` / `autoAlpha` | স্বচ্ছতা (`autoAlpha` ০ হলে `visibility:hidden` ও করে) |
| `duration` | কতক্ষণ (সেকেন্ড) |
| `delay` | দেরি |
| `ease` | গতির ধরন |
| `stagger` | একাধিক element এ একটার পর একটা |
| `repeat`, `yoyo` | বারবার চালানো / উল্টো চালানো |
| `onComplete` | শেষ হলে function |

**Ease এর ধারণা:** `"power3.out"` (সবচেয়ে বেশি ব্যবহৃত, মসৃণভাবে থামে), `"power2.inOut"`, `"back.out(1.7)"` (একটু ছাড়িয়ে ফিরে আসে), `"elastic.out"`, `"expo.out"`, `"none"` (সরলরেখা, scrub এর জন্য)।

**Stagger:**
```js
gsap.from(".card", { y: 60, opacity: 0, duration: 0.8, stagger: 0.15 });
gsap.from(".card", { y: 60, opacity: 0, stagger: { each: 0.1, from: "center" } });
```

**Timeline (ক্রমানুসারে animation):**
```js
const tl = gsap.timeline({ defaults: { duration: 0.8, ease: "power3.out" } });

tl.from(".logo", { y: -30, opacity: 0 })
  .from(".nav a", { y: -20, opacity: 0, stagger: 0.1 }, "-=0.4")   // আগেরটা শেষ হওয়ার ০.৪ সেকেন্ড আগে শুরু
  .from(".hero-title", { y: 80, opacity: 0 }, "<")                 // আগেরটার সাথে একসাথে শুরু
  .from(".hero-btn", { scale: 0.8, opacity: 0 }, "+=0.2");         // আগেরটা শেষের ০.২ সেকেন্ড পরে
```

Position parameter: `"<"` (আগেরটার শুরুতে), `">"` (আগেরটার শেষে), `"-=0.5"` (০.৫ সেকেন্ড আগে), `"+=0.5"` (০.৫ সেকেন্ড পরে), `"label"` (নামকরা জায়গা)।

### 12.4 React এ `useGSAP` (সবচেয়ে গুরুত্বপূর্ণ)

**কেন `useEffect` না?** React (বিশেষ করে StrictMode এ) effect দুইবার চালায়। GSAP animation cleanup না করলে duplicate animation, ভাঙা scroll position, memory leak হয়। `useGSAP` সব নিজে `revert` করে।

```jsx
import { useRef } from "react";
import gsap from "gsap";
import { useGSAP } from "@gsap/react";

gsap.registerPlugin(useGSAP);      // একবার register (বা আলাদা lib/gsap.js ফাইলে)

export default function Hero() {
  const container = useRef(null);

  useGSAP(
    () => {
      // ".title" শুধু এই container এর ভেতরের element ধরবে (scope এর কারণে)
      gsap.from(".title", { y: 60, opacity: 0, duration: 1, ease: "power3.out" });
      gsap.from(".subtitle", { y: 40, opacity: 0, delay: 0.3 });
    },
    { scope: container }           // ⭐ সবসময় scope দাও
  );

  return (
    <section ref={container}>
      <h1 className="title">Hello World</h1>
      <p className="subtitle">Welcome</p>
    </section>
  );
}
```

**একটা central ফাইলে register করা (পরিষ্কার):**
```js
// src/lib/gsap.js
import gsap from "gsap";
import { useGSAP } from "@gsap/react";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { SplitText } from "gsap/SplitText";
import { Flip } from "gsap/Flip";

gsap.registerPlugin(useGSAP, ScrollTrigger, SplitText, Flip);
export { gsap, useGSAP, ScrollTrigger, SplitText, Flip };

// অন্য ফাইলে: import { gsap, useGSAP } from "@/lib/gsap";
```

**State/props বদলালে animation আবার চালানো (`dependencies`):**
```jsx
useGSAP(() => {
  gsap.to(".bar", { width: `${progress}%`, duration: 0.6 });
}, { scope: container, dependencies: [progress], revertOnUpdate: true });
```

**Click/hover (event handler) এর animation — `contextSafe`:**
```jsx
const container = useRef(null);
const { contextSafe } = useGSAP({ scope: container });

const onEnter = contextSafe(() => gsap.to(".icon", { rotation: 360, duration: 0.6 }));
const onLeave = contextSafe(() => gsap.to(".icon", { rotation: 0, duration: 0.6 }));

return (
  <div ref={container}>
    <button onMouseEnter={onEnter} onMouseLeave={onLeave}>
      <span className="icon">★</span>
    </button>
  </div>
);
```
`contextSafe` ছাড়া event handler এ তৈরি animation component unmount হলে cleanup হয় না।

**Timeline কে ref এ রেখে নিয়ন্ত্রণ (play/reverse):**
```jsx
const tl = useRef();
const container = useRef();

useGSAP(() => {
  tl.current = gsap.timeline({ paused: true })
    .to(".menu", { x: 0, duration: 0.5, ease: "power3.out" })
    .from(".menu li", { x: 40, opacity: 0, stagger: 0.08 }, "-=0.2");
}, { scope: container });

const toggle = () => (isOpen ? tl.current.reverse() : tl.current.play());
```

### 12.5 ScrollTrigger (scroll এর সাথে animation)

```jsx
import { gsap, useGSAP, ScrollTrigger } from "@/lib/gsap";

export default function Features() {
  const container = useRef(null);

  useGSAP(() => {
    // ১) স্ক্রলে দেখা গেলে চালু
    gsap.from(".card", {
      y: 80,
      opacity: 0,
      duration: 0.8,
      stagger: 0.15,
      scrollTrigger: {
        trigger: ".cards",            // কোন element দেখে শুরু হবে
        start: "top 80%",             // element এর top যখন viewport এর ৮০% এ পৌঁছায়
        toggleActions: "play none none reverse",
        // markers: true,             // debug এর সময় দাগ দেখায়, শেষে মুছো
      },
    });

    // ২) Scrub: স্ক্রলের সাথে সাথে animation এগোয়/পেছায়
    gsap.to(".progress", {
      scaleX: 1,
      ease: "none",                   // scrub এ ease সাধারণত none
      scrollTrigger: { trigger: container.current, start: "top top", end: "bottom bottom", scrub: true },
    });

    // ৩) Pin: স্ক্রলের সময় element আটকে রাখা
    gsap.timeline({
      scrollTrigger: { trigger: ".story", start: "top top", end: "+=2000", scrub: 1, pin: true },
    })
      .from(".step1", { opacity: 0, y: 50 })
      .from(".step2", { opacity: 0, y: 50 })
      .from(".step3", { opacity: 0, y: 50 });
  }, { scope: container });

  return <section ref={container}>...</section>;
}
```

**`start`/`end` পড়ার নিয়ম:** `"top 80%"` = "trigger এর **top** যখন viewport এর **৮০%** জায়গায় আসে"। প্রথম শব্দ trigger এর জায়গা, দ্বিতীয়টা viewport এর জায়গা।

**`toggleActions`:** চারটা মান — `onEnter onLeave onEnterBack onLeaveBack`। মান: `play`, `pause`, `resume`, `reset`, `restart`, `complete`, `reverse`, `none`।

**Horizontal scroll (জনপ্রিয় ইফেক্ট):**
```jsx
useGSAP(() => {
  const track = container.current.querySelector(".track");
  gsap.to(track, {
    x: () => -(track.scrollWidth - window.innerWidth),
    ease: "none",
    scrollTrigger: {
      trigger: container.current,
      pin: true,
      scrub: 1,
      end: () => "+=" + track.scrollWidth,
      invalidateOnRefresh: true,     // resize এ আবার হিসাব
    },
  });
}, { scope: container });
```

### 12.6 Responsive: `gsap.matchMedia()`

Mobile ও desktop এ আলাদা animation:

```jsx
useGSAP(() => {
  const mm = gsap.matchMedia();

  mm.add("(min-width: 768px)", () => {
    // শুধু desktop: pin ও বড় animation
    gsap.from(".hero", { x: -200, opacity: 0, scrollTrigger: { trigger: ".hero", scrub: true } });
  });

  mm.add("(max-width: 767px)", () => {
    // mobile: সহজ fade
    gsap.from(".hero", { opacity: 0, y: 30 });
  });

  mm.add("(prefers-reduced-motion: reduce)", () => {
    gsap.set(".hero", { clearProps: "all" });   // যারা animation চান না তাদের জন্য বন্ধ
  });
}, { scope: container });
```

### 12.7 Lenis দিয়ে Smooth Scroll (GSAP সাথে)

```jsx
// src/components/SmoothScroll.jsx
import { useEffect } from "react";
import Lenis from "lenis";
import { gsap, ScrollTrigger } from "@/lib/gsap";

export default function SmoothScroll({ children }) {
  useEffect(() => {
    const lenis = new Lenis();

    lenis.on("scroll", ScrollTrigger.update);           // scroll হলে ScrollTrigger কে জানাও

    const tick = (time) => lenis.raf(time * 1000);      // GSAP এর ticker দিয়ে Lenis চালাও
    gsap.ticker.add(tick);
    gsap.ticker.lagSmoothing(0);

    return () => {
      gsap.ticker.remove(tick);
      lenis.destroy();
    };
  }, []);

  return children;
}

// main.jsx / App.jsx এ: <SmoothScroll><RouterProvider .../></SmoothScroll>
```

### 12.8 SplitText (অক্ষর/শব্দ/লাইন ধরে animation)

```jsx
import { gsap, useGSAP, SplitText } from "@/lib/gsap";

useGSAP(() => {
  const split = SplitText.create(".headline", { type: "chars,words" });

  gsap.from(split.chars, {
    y: 40,
    opacity: 0,
    rotateX: -90,
    duration: 0.6,
    ease: "back.out(1.7)",
    stagger: 0.03,
  });
}, { scope: container });
```

> **Bangla লেখায় সতর্কতা:** বাংলা যুক্তাক্ষর/স্বরচিহ্ন অক্ষর (`chars`) ধরে ভাঙলে ভেঙে যেতে পারে। বাংলায় `type: "words"` বা `"lines"` ব্যবহার করো, আর পরীক্ষা করে নাও।
> SplitText এর API নতুন version এ আপডেট হয়েছে, তাই gsap.com এর docs দেখে নিও।

### 12.9 Flip (এক অবস্থা থেকে আরেক অবস্থায় মসৃণ বদল)

Layout বদলালে (grid → list, filter) element গুলো জাদুর মতো নিজের নতুন জায়গায় যায়:

```jsx
const { contextSafe } = useGSAP({ scope: container });

const toggleLayout = contextSafe(() => {
  const state = Flip.getState(".item");     // ১) আগের অবস্থা ধরো
  setIsGrid((g) => !g);                     // ২) layout বদলাও (React state)
  requestAnimationFrame(() => {
    Flip.from(state, { duration: 0.6, ease: "power2.inOut", stagger: 0.03, absolute: true });  // ৩) মসৃণ বদল
  });
});
```

### 12.10 সাধারণ কিছু Pattern

**Page load এর Hero animation:**
```jsx
useGSAP(() => {
  gsap.timeline({ defaults: { ease: "power3.out", duration: 1 } })
    .from(".hero-bg", { scale: 1.2, duration: 1.8 })
    .from(".hero-title span", { yPercent: 100, stagger: 0.1 }, "-=1.2")
    .from(".hero-sub", { y: 30, opacity: 0 }, "-=0.6")
    .from(".hero-cta", { scale: 0.8, opacity: 0 }, "-=0.5");
}, { scope: container });
```

**Parallax:**
```js
gsap.to(".bg-image", {
  yPercent: 30,
  ease: "none",
  scrollTrigger: { trigger: ".section", start: "top bottom", end: "bottom top", scrub: true },
});
```

**Counter (সংখ্যা গোনা):**
```js
const obj = { val: 0 };
gsap.to(obj, {
  val: 1500, duration: 2, ease: "power1.out",
  scrollTrigger: { trigger: ".stats", start: "top 80%", once: true },
  onUpdate: () => (counterRef.current.textContent = Math.floor(obj.val).toLocaleString()),
});
```
(এখানে React state এ প্রতি frame এ setState না করে সরাসরি DOM এ লেখা হয়েছে, তাই দ্রুত।)

**Magnetic button / Cursor follower:** `gsap.quickTo(el, "x", { duration: 0.4 })` — mousemove এ বারবার তৈরি না করে দ্রুত update।

**Route বদলের পর ScrollTrigger refresh:**
```jsx
const { pathname } = useLocation();
useEffect(() => {
  const id = setTimeout(() => ScrollTrigger.refresh(), 100);
  return () => clearTimeout(id);
}, [pathname]);
```

### 12.11 Performance ও সাধারণ ভুল

**Performance:**
- শুধু `x, y, scale, rotation, opacity` animate করো (GPU-তে চলে)। `width, height, top, left, margin` animate করা ধীর (layout হিসাব করতে হয়)।
- অনেক element এ `will-change: transform` (কেবল দরকারে, সব জায়গায় না)।
- Mobile এ ভারী scrub/pin কমাও (`matchMedia`)।
- ছবি লোড হওয়ার পর `ScrollTrigger.refresh()` লাগতে পারে।

**সাধারণ ভুল ও সমাধান:**

| সমস্যা | কারণ ও সমাধান |
|---|---|
| Animation দুইবার চলছে | `useEffect` ব্যবহার করেছ → `useGSAP` নাও |
| অন্য component এর element নড়ছে | `scope` দাওনি |
| Element জ্বলে উঠে তারপর animate (FOUC) | শুরুতেই CSS এ `opacity: 0` দাও বা `autoAlpha` ব্যবহার করো |
| Scroll position ভুল জায়গায় ট্রিগার | `ScrollTrigger.refresh()`, ছবি/ফন্ট লোডের পর |
| Pin করলে layout লাফাচ্ছে | `pinSpacing`, parent এ `overflow: hidden` আছে কিনা দেখো |
| `markers: true` production এ থেকে গেছে | শেষে মুছো |
| Event handler এর animation cleanup হচ্ছে না | `contextSafe` ব্যবহার করো |
| `.card` selector ধরছে না | element তখনও DOM এ নেই (conditional render), অথবা ref ব্যবহার করো |
| Conditional rendered element animate হচ্ছে না | state বদলের পর `dependencies` দিয়ে আবার চালাও |

### 12.12 `motion` (framer-motion) কখন ভালো

```bash
npm i motion
```

```jsx
import { motion, AnimatePresence } from "motion/react";

<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  exit={{ opacity: 0 }}
  whileHover={{ scale: 1.05 }}
  transition={{ duration: 0.4 }}
/>

// Mount/unmount animation (modal, toast) — GSAP এ এটা কঠিন, motion এ সহজ
<AnimatePresence>{open && <motion.div exit={{ opacity: 0 }} ... />}</AnimatePresence>
```

**নিয়ম:** UI component এর ছোট animation, modal, list reorder → `motion`। বড় সিনেমাটিক/scroll/timeline → GSAP। একই element এ দুটো মিশিও না।

---

## 13. SEO, i18n, Error Boundary, Performance

### SEO (React SPA এ)

React 19 এ component এর ভেতরেই লেখা যায়:
```jsx
export default function ProductPage() {
  return (
    <>
      <title>Product | My Shop</title>
      <meta name="description" content="..." />
      {/* page content */}
    </>
  );
}
```
পুরনো React এ `react-helmet-async`।

> ⚠️ SPA তে social share (Facebook/WhatsApp preview) ও Google index দুর্বল থাকে। SEO গুরুত্বপূর্ণ হলে **Next.js** নাও (Part 2)।

### বহুভাষা (বাংলা/English)

```bash
npm i i18next react-i18next
```

```js
// src/lib/i18n.js
import i18n from "i18next";
import { initReactI18next } from "react-i18next";

i18n.use(initReactI18next).init({
  lng: "bn",
  fallbackLng: "en",
  resources: {
    en: { translation: { welcome: "Welcome", cart: "Cart ({{count}})" } },
    bn: { translation: { welcome: "স্বাগতম", cart: "কার্ট ({{count}})" } },
  },
});
export default i18n;
```

```jsx
import { useTranslation } from "react-i18next";
const { t, i18n } = useTranslation();
<h1>{t("welcome")}</h1>
<button onClick={() => i18n.changeLanguage("en")}>English</button>
```

### Error Boundary

কোনো component ভেঙে গেলে পুরো app সাদা হয়ে যাওয়া ঠেকায়।

```bash
npm i react-error-boundary
```

```jsx
import { ErrorBoundary } from "react-error-boundary";

<ErrorBoundary
  fallbackRender={({ error, resetErrorBoundary }) => (
    <div>
      <p>কিছু ভুল হয়েছে: {error.message}</p>
      <button onClick={resetErrorBoundary}>আবার চেষ্টা করো</button>
    </div>
  )}
  onError={(error) => Sentry.captureException(error)}
>
  <Dashboard />
</ErrorBoundary>
```

### Performance

```jsx
// ১) Code splitting: ভারী পেজ/component দরকারে লোড
import { lazy, Suspense } from "react";
const Chart = lazy(() => import("./Chart"));
<Suspense fallback={<Spinner />}><Chart /></Suspense>

// ২) memo/useMemo/useCallback: মাপার পর দরকার হলে (সবখানে না)
const ProductCard = memo(function ProductCard({ product }) { ... });
const sorted = useMemo(() => items.toSorted(compare), [items]);

// ৩) বড় list: virtualize
// npm i @tanstack/react-virtual

// ৪) ছবি: loading="lazy", সঠিক মাপ, webp/avif
<img src={url} alt="..." loading="lazy" width="300" height="200" />

// ৫) Bundle দেখা
// npm i -D rollup-plugin-visualizer
```

**নিয়ম:** আগে মাপো (React DevTools Profiler, Lighthouse), তারপর optimize করো। অকারণে সব জায়গায় `useMemo` লাগিও না।

---

## 14. Testing ও Dev Tools

```bash
npm i -D vitest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event
npm i -D msw
npm i -D @playwright/test
npm i -D eslint prettier prettier-plugin-tailwindcss husky lint-staged
```

```js
// vite.config.js এ যোগ
test: { environment: "jsdom", setupFiles: "./src/test/setup.js", globals: true }
```
```js
// src/test/setup.js
import "@testing-library/jest-dom/vitest";
```

```jsx
// LoginForm.test.jsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { it, expect } from "vitest";
import LoginForm from "./LoginForm";

it("ভুল email দিলে error দেখায়", async () => {
  render(<LoginForm />);
  await userEvent.type(screen.getByPlaceholderText("Email"), "abc");
  await userEvent.click(screen.getByRole("button", { name: /login/i }));
  expect(await screen.findByText("সঠিক email দাও")).toBeInTheDocument();
});
```

| টুল | কাজ |
|---|---|
| `vitest` + Testing Library | component test |
| `msw` | API mock (আসল backend ছাড়াই) |
| `playwright` | E2E: browser এ login → কেনা পুরো flow |
| `eslint` + `prettier` | code মান ও ফরম্যাট |
| `husky` + `lint-staged` | commit এর আগে অটো check |
| React DevTools | component ও re-render দেখা |
| Storybook | UI component আলাদা করে দেখা (বড় টিমে) |

---

## 15. Build ও Deploy (React)

```bash
npm run build      # dist/ ফোল্ডার তৈরি
npm run preview    # build করা app লোকালি দেখা
```

**Vercel / Netlify / Cloudflare Pages:** GitHub repo যুক্ত করলেই auto deploy। Environment variable ড্যাশবোর্ডে দাও।

**SPA এর জন্য জরুরি — রিফ্রেশে 404 ঠেকানো (rewrite):**
```json
// vercel.json
{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }
```
```
# Netlify: public/_redirects
/*  /index.html  200
```

**Nginx এ নিজের VPS:**
```nginx
location / { try_files $uri /index.html; }
location /api/ { proxy_pass http://localhost:5000; }   # একই domain এ API, cookie ও CORS সমস্যা নেই
```

Deploy এর আগে: `VITE_API_URL` production মান, backend এর `CLIENT_URL`/CORS origin, HTTPS, ও cookie `secure` সেটিং মিলিয়ে নাও।

---

## 16. React এর Quick Install Cheat Sheet

```bash
# প্রজেক্ট
npm create vite@latest my-app -- --template react
cd my-app

# Routing, Data, State
npm i react-router axios @tanstack/react-query zustand

# Form
npm i react-hook-form zod @hookform/resolvers

# Styling
npm i tailwindcss @tailwindcss/vite clsx tailwind-merge class-variance-authority lucide-react sonner next-themes
npx shadcn@latest init

# Utility
npm i use-debounce usehooks-ts react-intersection-observer dayjs react-error-boundary react-dropzone socket.io-client

# Animation
npm i gsap @gsap/react lenis motion

# Dev
npm i -D @tanstack/react-query-devtools prettier prettier-plugin-tailwindcss vitest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event msw
```

**Table/Chart/অন্যান্য (দরকার হলে):**
```bash
npm i @tanstack/react-table recharts @dnd-kit/core @dnd-kit/sortable i18next react-i18next @tiptap/react @tiptap/starter-kit
```


---
---

# 🟧 PART 2 — Next.js (App Router)

> React ভালো করে শেখার পর এই অংশে এসো। Part 1 এর প্রায় সব package (React Query, Zustand, react-hook-form, zod, GSAP, Tailwind, shadcn) Next.js এও একই চলে। নতুন শুধু **Server/Client ভাগ, ফাইল-ভিত্তিক routing ও server-side data**।
>
> ⚠️ Next.js দ্রুত বদলায়। নিচের কোডে যেখানে version ভেদে পার্থক্য আছে সেখানে নোট দিয়েছি। কাজ শুরুর আগে nextjs.org/docs এ একবার মিলিয়ে নিও।

## 17. Next.js কী ও কেন

Next.js হলো React এর উপরে তৈরি framework। React যা দেয় না (routing, server rendering, SEO, image optimization, API route) সেগুলো Next.js সব দেয়।

### Server Component vs Client Component (সবচেয়ে গুরুত্বপূর্ণ ধারণা)

App Router এ **প্রতিটি component default এ Server Component**।

| | Server Component (default) | Client Component (`"use client"`) |
|---|---|---|
| কোথায় চলে | Server এ | Browser এ (প্রথমে server এ HTML ও হয়) |
| `useState`, `useEffect`, event (`onClick`) | ❌ | ✅ |
| Browser API (`window`, `localStorage`) | ❌ | ✅ |
| সরাসরি DB/backend/secret ব্যবহার | ✅ | ❌ |
| JS bundle এ যায় | না (ছোট থাকে) | হ্যাঁ |
| GSAP, React Query, Zustand, react-hook-form | ❌ | ✅ |

```jsx
// Server Component (default) — async হতে পারে, সরাসরি data আনে
export default async function Page() {
  const products = await getProducts();       // server এ চলে
  return <ProductList products={products} />;
}
```

```jsx
// Client Component — ফাইলের একদম উপরে "use client"
"use client";
import { useState } from "react";

export default function Counter() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

**নিয়ম:**
1. যতটা সম্ভব Server Component রাখো, শুধু interactivity লাগলে Client Component বানাও।
2. `"use client"` যত নিচের (ছোট) component এ দেবে তত ভালো। পুরো পেজে দিও না।
3. Server Component এর ভেতরে Client Component import করা যায়। উল্টোটা সরাসরি না, তবে `children` হিসেবে পাঠানো যায়।
4. Server → Client এ props পাঠালে তা serializable হতে হবে (function, class instance পাঠানো যায় না)।

---

## 18. সেটআপ ও ফোল্ডার স্ট্রাকচার

```bash
npx create-next-app@latest my-app
```

প্রশ্নগুলো: TypeScript (এই গাইডে No ধরা হয়েছে), ESLint, Tailwind, `src/` directory, **App Router (Yes)**, Turbopack, import alias (`@/*`)।

```bash
cd my-app
npm run dev      # http://localhost:3000
```

```
my-app/
├── app/
│   ├── layout.js            # root layout (সব পেজে)
│   ├── page.js              # "/" পেজ
│   ├── loading.js           # লোডিং UI
│   ├── error.js             # error UI ("use client" লাগে)
│   ├── not-found.js         # 404
│   ├── globals.css
│   ├── providers.js         # React Query, Theme ইত্যাদি ("use client")
│   ├── (auth)/              # route group: URL এ আসে না, শুধু ভাগ করে
│   │   ├── login/page.js            # /login
│   │   └── register/page.js         # /register
│   ├── (dashboard)/
│   │   ├── layout.js                # dashboard এর আলাদা layout
│   │   └── dashboard/page.js        # /dashboard
│   ├── products/
│   │   ├── page.js                  # /products
│   │   └── [id]/page.js             # /products/123 (dynamic)
│   └── api/
│       └── health/route.js          # GET /api/health (Route Handler)
├── components/
│   ├── ui/                  # shadcn component
│   └── ...
├── lib/                     # api.js, utils.js, auth.js
├── hooks/
├── actions/                 # Server Actions
├── public/
├── middleware.js            # (Next 16 এ নাম proxy.js, নিচে দেখো)
├── next.config.mjs
├── .env.local
└── package.json
```

### Environment Variable

```
# .env.local
API_URL=http://localhost:5000/api/v1               # শুধু server এ (secret থাকতে পারে)
NEXT_PUBLIC_API_URL=http://localhost:5000/api/v1   # browser এও যাবে (সবাই দেখতে পাবে)
JWT_SECRET=...                                     # শুধু server
```

> `NEXT_PUBLIC_` prefix ছাড়া variable browser এ পৌঁছায় না। Secret কখনো `NEXT_PUBLIC_` দিয়ে লিখো না।

---

## 19. Routing ও বিশেষ ফাইল

### ফাইলের নাম = কাজ

| ফাইল | কাজ |
|---|---|
| `page.js` | সেই URL এর পেজ (এটা না থাকলে URL তৈরি হয় না) |
| `layout.js` | ভাগ করা কাঠামো (navbar/footer)। navigate করলে re-render হয় না |
| `template.js` | layout এর মতো, কিন্তু প্রতি navigation এ নতুন করে mount হয় (page transition এ কাজের) |
| `loading.js` | data আসার সময় অটো skeleton/spinner (Suspense) |
| `error.js` | error হলে দেখানো UI (`"use client"` বাধ্যতামূলক) |
| `not-found.js` | 404 (`notFound()` call করলেও) |
| `route.js` | API endpoint (GET/POST ইত্যাদি) |

### Root Layout

```jsx
// app/layout.js
import "./globals.css";
import Providers from "./providers";
import { Hind_Siliguri } from "next/font/google";

const font = Hind_Siliguri({ subsets: ["bengali", "latin"], weight: ["400", "500", "700"] });

export const metadata = {
  title: { default: "My App", template: "%s | My App" },
  description: "আমার অ্যাপ",
};

export default function RootLayout({ children }) {
  return (
    <html lang="bn" suppressHydrationWarning>
      <body className={font.className}>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

### Dynamic Route ও searchParams

```jsx
// app/products/[id]/page.js
import { notFound } from "next/navigation";

export default async function ProductPage({ params }) {
  const { id } = await params;       // ⚠️ নতুন version এ params একটা Promise, await করতে হয়
  const res = await fetch(`${process.env.API_URL}/products/${id}`);
  if (!res.ok) notFound();
  const { data: product } = await res.json();
  return <h1>{product.name}</h1>;
}
```

```jsx
// app/products/page.js  (?page=2&search=shoe)
export default async function Products({ searchParams }) {
  const { page = "1", search = "" } = await searchParams;   // এটাও Promise
  ...
}
```

> Next 14 পর্যন্ত `params`/`searchParams` সরাসরি object ছিল, Next 15 থেকে Promise। পুরনো tutorial দেখলে এই পার্থক্য খেয়াল রাখো।

### Navigation (Client Component এ)

```jsx
"use client";
import Link from "next/link";
import { useRouter, usePathname, useSearchParams, useParams } from "next/navigation";
//                ⚠️ next/router না, next/navigation (App Router এ)

<Link href="/products">Products</Link>            // react-router এর to="" এর বদলে href=""
<Link href={`/products/${id}`} prefetch>View</Link>

const router = useRouter();
router.push("/dashboard");       // navigate
router.replace("/login");        // history না রেখে
router.back();
router.refresh();                // server data আবার আনা

const pathname = usePathname();          // "/products"
const searchParams = useSearchParams();  // ?page=2 → searchParams.get("page")
const { id } = useParams();              // client এ dynamic param
```

Server Component এ redirect: `import { redirect } from "next/navigation"; redirect("/login");`

### Loading ও Error

```jsx
// app/products/loading.js — products পেজ লোড হওয়ার সময় অটো দেখায়
export default function Loading() {
  return <div className="animate-pulse">Loading...</div>;
}
```

```jsx
// app/products/error.js
"use client";
export default function Error({ error, reset }) {
  return (
    <div>
      <p>কিছু ভুল হয়েছে</p>
      <button onClick={() => reset()}>আবার চেষ্টা করো</button>
    </div>
  );
}
```

### Route Handler (Next এর ভেতরেই API)

```js
// app/api/health/route.js
import { NextResponse } from "next/server";

export async function GET() {
  return NextResponse.json({ status: "ok" });
}

export async function POST(request) {
  const body = await request.json();
  return NextResponse.json({ received: body }, { status: 201 });
}
```

> তোমার তো আলাদা Express backend আছে। তাই Route Handler মূলত ছোট কাজে (webhook, proxy) লাগবে, পুরো backend এখানে বানানোর দরকার নেই।

---

## 20. Data Fetching, Caching, Server Actions

### Server Component এ সরাসরি fetch

```jsx
// app/products/page.js
async function getProducts(page) {
  const res = await fetch(`${process.env.API_URL}/products?page=${page}`, {
    next: { revalidate: 60 },     // ৬০ সেকেন্ড cache, তারপর পুরনো দেখিয়ে পেছনে নতুন আনে (ISR)
    // cache: "no-store",         // সবসময় নতুন (SSR)
  });
  if (!res.ok) throw new Error("Products আনা যায়নি");   // error.js ধরবে
  return res.json();
}

export default async function Products({ searchParams }) {
  const { page = "1" } = await searchParams;
  const { data, totalPages } = await getProducts(page);

  return (
    <ul>
      {data.map((p) => <li key={p._id}>{p.name} — ৳{p.price}</li>)}
    </ul>
  );
}
```

> ⚠️ **Caching এর নিয়ম Next এর version ভেদে বদলেছে** (Next 14 এ fetch default cache হতো, 15 থেকে হয় না, আর নতুন version এ আরও নতুন caching মডেল এসেছে)। তাই সবসময় `revalidate` বা `cache: "no-store"` **স্পষ্ট করে লিখে দাও**, আর docs এর "Caching" পেজ দেখে নাও।

**একাধিক fetch একসাথে (দ্রুত):**
```jsx
const [products, categories] = await Promise.all([getProducts(), getCategories()]);
```
একটার পর একটা `await` করলে ধীর হয় (waterfall)।

**Loading UI ভাগ করে (Suspense streaming):**
```jsx
import { Suspense } from "react";

export default function Page() {
  return (
    <>
      <h1>Dashboard</h1>                         {/* সঙ্গে সঙ্গে দেখায় */}
      <Suspense fallback={<p>Loading chart...</p>}>
        <SalesChart />                           {/* async Server Component, তৈরি হলে আসে */}
      </Suspense>
    </>
  );
}
```

### Cache ফেলে দেওয়া (revalidate)

```js
import { revalidatePath, revalidateTag } from "next/cache";
revalidatePath("/products");            // এই পেজের cache পুরনো
// fetch(url, { next: { tags: ["products"] } }) করা থাকলে:
revalidateTag("products");
```

### Server Actions (ফর্ম submit, Server এ চলে)

```js
// actions/product.actions.js
"use server";

import { cookies } from "next/headers";
import { revalidatePath } from "next/cache";
import { z } from "zod";

const schema = z.object({
  name: z.string().min(2, "নাম কমপক্ষে ২ অক্ষর"),
  price: z.coerce.number().positive("দাম ০ এর বেশি"),
});

export async function createProduct(prevState, formData) {
  // ১) সবসময় server এও validate করো (client validation এড়িয়ে call করা যায়)
  const parsed = schema.safeParse(Object.fromEntries(formData));
  if (!parsed.success) {
    return { errors: parsed.error.flatten().fieldErrors };
  }

  // ২) cookie পাঠিয়ে Express backend এ request
  const cookieStore = await cookies();      // ⚠️ নতুন version এ await লাগে
  const res = await fetch(`${process.env.API_URL}/products`, {
    method: "POST",
    headers: { "Content-Type": "application/json", Cookie: cookieStore.toString() },
    body: JSON.stringify(parsed.data),
  });

  if (!res.ok) {
    const err = await res.json().catch(() => ({}));
    return { message: err.message || "তৈরি করা যায়নি" };
  }

  revalidatePath("/products");
  return { success: true };
}
```

```jsx
// components/ProductForm.js
"use client";
import { useActionState } from "react";       // React 19
import { useFormStatus } from "react-dom";
import { createProduct } from "@/actions/product.actions";

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>{pending ? "Saving..." : "Save"}</button>;
}

export default function ProductForm() {
  const [state, formAction] = useActionState(createProduct, null);

  return (
    <form action={formAction}>
      <input name="name" placeholder="Name" />
      {state?.errors?.name && <p className="text-red-500">{state.errors.name[0]}</p>}

      <input name="price" type="number" placeholder="Price" />
      {state?.errors?.price && <p className="text-red-500">{state.errors.price[0]}</p>}

      {state?.message && <p className="text-red-500">{state.message}</p>}
      {state?.success && <p className="text-green-600">সফল!</p>}
      <SubmitButton />
    </form>
  );
}
```

**Server Action নাকি react-hook-form + React Query?** দুটোই চলে।
- সাধারণ ফর্ম, SEO/progressive enhancement → Server Action
- জটিল ফর্ম (dynamic field, লাইভ validation), অনেক client interaction → react-hook-form + React Query (`mutate` দিয়ে সরাসরি Express এ)
- দুটো মিশিয়েও চলে: react-hook-form এর `handleSubmit` এ server action কল।

> Server Action আসলে **public POST endpoint**। ভেতরে সবসময় auth ও validation চেক করো।

---

## 21. Express Backend এর সাথে Auth ও API

### ধাপ ১: কীভাবে frontend ও backend কথা বলবে?

| পদ্ধতি | কীভাবে | সুবিধা/অসুবিধা |
|---|---|---|
| **A) Browser সরাসরি Express এ** | `axios` + `withCredentials`, CORS চালু | সহজ, কিন্তু আলাদা domain হলে cookie/SameSite ঝামেলা |
| **B) Next এর `rewrites` দিয়ে proxy (প্রস্তাবিত)** | Browser শুধু Next এর domain এ call করে, Next পেছনে Express এ পাঠায় | CORS লাগে না, cookie "first-party" থাকে, সবচেয়ে ঝামেলাহীন |

```js
// next.config.mjs
/** @type {import('next').NextConfig} */
const nextConfig = {
  async rewrites() {
    return [
      { source: "/api/:path*", destination: `${process.env.API_URL}/:path*` },
      // /api/auth/login → http://localhost:5000/api/v1/auth/login
    ];
  },
  images: {
    remotePatterns: [{ protocol: "https", hostname: "res.cloudinary.com" }],   // Cloudinary ছবির জন্য
  },
};
export default nextConfig;
```

এখন browser এ `axios` এর `baseURL: "/api"` দিলেই চলে। Express এর `Set-Cookie` Next এর domain এ বসে যায়।

### ধাপ ২: Server Component থেকে logged-in user এর data আনা

Server Component এ browser এর cookie অটো যায় না। হাতে forward করতে হয়:

```js
// lib/api.server.js
import "server-only";                     // ভুলে client এ import করলে build error দেবে
import { cookies } from "next/headers";

export async function serverFetch(path, options = {}) {
  const cookieStore = await cookies();
  const res = await fetch(`${process.env.API_URL}${path}`, {
    ...options,
    headers: { ...options.headers, Cookie: cookieStore.toString() },
    cache: "no-store",                    // user-specific data cache করো না
  });
  if (res.status === 401) return null;
  if (!res.ok) throw new Error(`API error ${res.status}`);
  return res.json();
}

export const getCurrentUser = async () => (await serverFetch("/auth/me"))?.data ?? null;
```

```jsx
// app/(dashboard)/dashboard/page.js
import { redirect } from "next/navigation";
import { getCurrentUser } from "@/lib/api.server";

export default async function Dashboard() {
  const user = await getCurrentUser();
  if (!user) redirect("/login");
  return <h1>স্বাগতম {user.name}</h1>;
}
```

### ধাপ ৩: Middleware/Proxy দিয়ে route রক্ষা (দ্রুত redirect)

```js
// middleware.js  (Next 16 এ ফাইল proxy.js, আর function এর নাম proxy — docs দেখো)
import { NextResponse } from "next/server";
import { jwtVerify } from "jose";                    // jsonwebtoken এখানে চলে না, jose লাগে

const secret = new TextEncoder().encode(process.env.JWT_SECRET);

export async function middleware(req) {
  const token = req.cookies.get("token")?.value;
  const loginUrl = new URL("/login", req.url);
  loginUrl.searchParams.set("from", req.nextUrl.pathname);

  if (!token) return NextResponse.redirect(loginUrl);
  try {
    await jwtVerify(token, secret);
    return NextResponse.next();
  } catch {
    return NextResponse.redirect(loginUrl);
  }
}

export const config = { matcher: ["/dashboard/:path*", "/account/:path*"] };
```

> ⚠️ **Middleware ই একমাত্র সুরক্ষা না।** এটা শুধু দ্রুত redirect এর জন্য। আসল নিরাপত্তা সবসময় (১) backend API তে token চেক ও (২) Server Component/Action এ user চেক করে রাখো।
> Middleware শুধু login আছে কিনা দেখে, role/permission এর বিস্তারিত চেক পেজ বা backend এ করো।

### ধাপ ৪: Login (Client Component)

```jsx
// app/(auth)/login/page.js
"use client";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { useRouter, useSearchParams } from "next/navigation";
import { api, getErrorMessage } from "@/lib/axios";

const schema = z.object({ email: z.string().email(), password: z.string().min(6) });

export default function LoginPage() {
  const router = useRouter();
  const params = useSearchParams();
  const { register, handleSubmit, setError, formState: { errors, isSubmitting } } = useForm({
    resolver: zodResolver(schema),
  });

  const onSubmit = async (values) => {
    try {
      await api.post("/auth/login", values);      // baseURL: "/api" (rewrites এর মাধ্যমে)
      router.replace(params.get("from") || "/dashboard");
      router.refresh();                           // Server Component এর data নতুন করে আনতে
    } catch (e) {
      setError("root", { message: getErrorMessage(e) });
    }
  };

  return <form onSubmit={handleSubmit(onSubmit)}>{/* ... সেকশন 8 এর মতো */}</form>;
}
```

> `useSearchParams()` ব্যবহার করা Client Component কে `<Suspense>` দিয়ে মুড়তে হতে পারে (build error এড়াতে)।

**Auth library চাইলে:** `next-auth` (Auth.js) বা `better-auth` — Google/GitHub login ও session সহজ করে। নিজের Express JWT থাকলে সাধারণত আলাদা library লাগে না।

---

## 22. Next.js এর নিজস্ব Package/Feature

| Package/Feature | কাজ |
|---|---|
| `next/image` | অটো optimize, lazy load, সঠিক মাপ। `<img>` এর বদলে |
| `next/font` | Google font self-host, layout shift নেই |
| `next/link` | client navigation ও prefetch |
| `next/navigation` | `useRouter`, `usePathname`, `redirect`, `notFound` |
| `next/headers` | `cookies()`, `headers()` (server) |
| `next/cache` | `revalidatePath`, `revalidateTag` |
| `next/dynamic` | Client component lazy load / `ssr: false` |
| `next/script` | third-party script (analytics) |
| `server-only` / `client-only` | ভুল environment এ import আটকায় |
| `jose` | middleware এ JWT verify |
| `next-themes` | dark mode |
| `next-intl` | বহুভাষা (বাংলা/English) |
| `nuqs` | URL query কে state এর মতো |
| `next-auth` / `better-auth` | OAuth/session |
| `next-safe-action` | type-safe Server Action |
| `@t3-oss/env-nextjs` | env validate |
| `@next/bundle-analyzer` | bundle মাপা |
| `@sentry/nextjs` | error tracking |
| `@vercel/analytics`, `posthog-js` | analytics |
| `next-sitemap` | sitemap (বা built-in `sitemap.js`) |

### `next/image`

```jsx
import Image from "next/image";

<Image src="/hero.jpg" alt="Hero" width={800} height={500} priority />   // priority: উপরের বড় ছবিতে
<Image src={product.image} alt={product.name} width={300} height={300} className="object-cover" />

// ছবির মাপ জানা না থাকলে
<div className="relative h-64 w-full">
  <Image src={url} alt="..." fill sizes="(max-width: 768px) 100vw, 33vw" className="object-cover" />
</div>
```
বাইরের domain (Cloudinary, S3) এর ছবি হলে `next.config` এ `remotePatterns` দিতে হবে (সেকশন 21 এ দেখানো আছে)।

### `next/dynamic` (ভারী client component lazy)

```jsx
"use client";
import dynamic from "next/dynamic";

const Chart = dynamic(() => import("@/components/Chart"), {
  ssr: false,                       // browser ছাড়া চলে না এমন library এর জন্য
  loading: () => <p>Loading...</p>,
});
```

### `next-intl` (বাংলা/English)

```bash
npm i next-intl
```
URL ভিত্তিক ভাষা (`/bn/products`, `/en/products`) ও translation ফাইল (`messages/bn.json`) সাজায়। Setup ধাপ বেশি ও version ভেদে বদলায়, তাই docs অনুসরণ করো।

---

## 23. React Query, Zustand, Form — Next.js এ

সবই Client Component এ চলে। Provider একবার বানিয়ে layout এ বসাও:

```jsx
// app/providers.js
"use client";
import { useState } from "react";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ThemeProvider } from "next-themes";
import { Toaster } from "sonner";

export default function Providers({ children }) {
  // useState দিয়ে বানাও, যাতে প্রতি render এ নতুন client না হয়
  const [queryClient] = useState(
    () => new QueryClient({ defaultOptions: { queries: { staleTime: 60_000, retry: 1 } } })
  );

  return (
    <QueryClientProvider client={queryClient}>
      <ThemeProvider attribute="class" defaultTheme="system" enableSystem>
        {children}
        <Toaster richColors />
      </ThemeProvider>
    </QueryClientProvider>
  );
}
```

- `useQuery`, `useMutation`, `zustand` store, `react-hook-form` — Part 1 (সেকশন 6, 7, 8) এর কোড **হুবহু** চলে, শুধু ফাইলের উপরে `"use client"` দাও।
- Zustand `persist` (localStorage) ব্যবহার করলে server আর browser এর প্রথম render এর মধ্যে hydration mismatch হতে পারে। সমাধান: `useEffect` দিয়ে mount এর পর দেখানো, অথবা `skipHydration` অপশন।

**কখন Server fetch, কখন React Query?**
- পেজের মূল data, SEO দরকার, একবার আনলেই চলে → **Server Component fetch**
- Live/বারবার বদলায়, ফিল্টার/infinite scroll/mutation, user interaction এর পর data → **React Query**
- দুটো একসাথেও চলে: প্রথম data server এ এনে `initialData` হিসেবে পাঠানো।

---

## 24. SEO ও Metadata

```jsx
// static
export const metadata = {
  title: "Products",
  description: "সব পণ্য দেখুন",
  openGraph: { title: "Products", images: ["/og.png"] },
};

// dynamic (প্রতিটি product এর আলাদা title/ছবি)
export async function generateMetadata({ params }) {
  const { id } = await params;
  const { data: p } = await (await fetch(`${process.env.API_URL}/products/${id}`)).json();
  return {
    title: p.name,
    description: p.description?.slice(0, 150),
    openGraph: { images: [p.image] },
  };
}
```

```js
// app/sitemap.js
export default async function sitemap() {
  const { data } = await (await fetch(`${process.env.API_URL}/products`)).json();
  return [
    { url: "https://myapp.com", lastModified: new Date() },
    ...data.map((p) => ({ url: `https://myapp.com/products/${p._id}`, lastModified: p.updatedAt })),
  ];
}
```

```js
// app/robots.js
export default function robots() {
  return { rules: { userAgent: "*", allow: "/", disallow: ["/dashboard"] }, sitemap: "https://myapp.com/sitemap.xml" };
}
```

SEO চেকলিস্ট: প্রতি পেজে আলাদা `title`/`description`, একটাই `<h1>`, ছবিতে `alt`, `next/image`, দ্রুত লোড (Lighthouse), sitemap/robots, structured data (JSON-LD)।

---

## 25. Rendering Strategy

| ধরন | মানে | কীভাবে | কখন |
|---|---|---|---|
| **SSG** (Static) | Build এর সময় HTML তৈরি | `generateStaticParams` / data না বদলালে default | Blog, landing, docs |
| **ISR** | Static, কিন্তু নির্দিষ্ট সময় পরপর নতুন | `fetch(..., { next: { revalidate: 60 } })` | Product list, news |
| **SSR** | প্রতি request এ server এ তৈরি | `cache: "no-store"` বা `cookies()` ব্যবহার | User-specific, সবসময় নতুন data |
| **CSR** | Browser এ data আনে | `"use client"` + React Query | Dashboard এর interactive অংশ |

```jsx
// SSG: জনপ্রিয় product আগে থেকে তৈরি
export async function generateStaticParams() {
  const { data } = await (await fetch(`${process.env.API_URL}/products?limit=50`)).json();
  return data.map((p) => ({ id: p._id }));
}
```

---

## 26. 🎬 GSAP — Next.js এ সম্পূর্ণ গাইড

[**GSAP Doc**](https://gsap.com/docs/v3/)-[**Next Doc**](https://nextjs.org/docs)

> আগে **সেকশন 12 (React এ GSAP)** পড়ো। GSAP এর সব ধারণা (tween, timeline, ScrollTrigger, SplitText, Flip, matchMedia) Next.js এও **হুবহু একই**। এখানে শুধু Next.js এর বিশেষ বিষয়গুলো।

### 26.1 মূল নিয়ম

1. GSAP browser এ চলে (DOM লাগে) → animation এর component এ **`"use client"`** দিতেই হবে।
2. Server Component এ GSAP code লিখো না।
3. `useGSAP` hook Next.js এর SSR এ নিরাপদ (ভেতরে isomorphic layout effect ব্যবহার করে)।

```bash
npm i gsap @gsap/react lenis
```

### 26.2 Central GSAP ফাইল

```js
// lib/gsap.js
"use client";
import gsap from "gsap";
import { useGSAP } from "@gsap/react";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { SplitText } from "gsap/SplitText";

gsap.registerPlugin(useGSAP, ScrollTrigger, SplitText);

export { gsap, useGSAP, ScrollTrigger, SplitText };
```

### 26.3 Client Component এ animation

```jsx
// components/Hero.js
"use client";

import { useRef } from "react";
import { gsap, useGSAP } from "@/lib/gsap";

export default function Hero() {
  const container = useRef(null);

  useGSAP(() => {
    gsap.timeline({ defaults: { ease: "power3.out", duration: 1 } })
      .from(".hero-title", { y: 80, opacity: 0 })
      .from(".hero-sub", { y: 40, opacity: 0 }, "-=0.6")
      .from(".hero-cta", { scale: 0.8, opacity: 0 }, "-=0.5");
  }, { scope: container });

  return (
    <section ref={container}>
      <h1 className="hero-title">Welcome</h1>
      <p className="hero-sub">Built with Next.js & GSAP</p>
      <button className="hero-cta">Get Started</button>
    </section>
  );
}
```

```jsx
// app/page.js — এটা Server Component, Hero (Client) কে সরাসরি import করা যায়
import Hero from "@/components/Hero";
import { getProducts } from "@/lib/data";

export default async function Home() {
  const products = await getProducts();    // server এ data
  return (
    <>
      <Hero />                              {/* শুধু এই অংশ browser এ JS পাঠায় */}
      <ProductList products={products} />
    </>
  );
}
```

**কৌশল:** পুরো পেজকে `"use client"` না করে শুধু animated অংশটুকু আলাদা Client Component বানাও। বাকি পেজ Server Component থাকলে SEO ও speed ভালো থাকে।

### 26.4 Server Component এর content animate করা (children pattern)

Server এ আনা data কে GSAP দিয়ে animate করতে চাইলে wrapper Client Component বানাও:

```jsx
// components/FadeIn.js
"use client";
import { useRef } from "react";
import { gsap, useGSAP } from "@/lib/gsap";

export default function FadeIn({ children, delay = 0, y = 40 }) {
  const ref = useRef(null);

  useGSAP(() => {
    gsap.from(ref.current, {
      y, opacity: 0, duration: 0.8, delay, ease: "power3.out",
      scrollTrigger: { trigger: ref.current, start: "top 85%", once: true },
    });
  }, { scope: ref });

  return <div ref={ref}>{children}</div>;
}
```

```jsx
// app/products/page.js (Server Component)
import FadeIn from "@/components/FadeIn";

export default async function Products() {
  const { data } = await getProducts();
  return data.map((p, i) => (
    <FadeIn key={p._id} delay={i * 0.08}>
      <ProductCard product={p} />          {/* ProductCard Server Component ও হতে পারে */}
    </FadeIn>
  ));
}
```

এই wrapper pattern সবচেয়ে কাজের: `FadeIn`, `Reveal`, `Parallax`, `Marquee` — একবার বানাও, সব জায়গায় ব্যবহার করো।

### 26.5 Smooth Scroll (Lenis) — Layout এ

```jsx
// components/SmoothScroll.js
"use client";
import { useEffect } from "react";
import Lenis from "lenis";
import { gsap, ScrollTrigger } from "@/lib/gsap";

export default function SmoothScroll({ children }) {
  useEffect(() => {
    const lenis = new Lenis();
    lenis.on("scroll", ScrollTrigger.update);

    const tick = (time) => lenis.raf(time * 1000);
    gsap.ticker.add(tick);
    gsap.ticker.lagSmoothing(0);

    return () => {
      gsap.ticker.remove(tick);
      lenis.destroy();
    };
  }, []);

  return children;
}
```

```jsx
// app/layout.js
import SmoothScroll from "@/components/SmoothScroll";
import Providers from "./providers";

export default function RootLayout({ children }) {
  return (
    <html lang="bn">
      <body>
        <Providers>
          <SmoothScroll>{children}</SmoothScroll>
        </Providers>
      </body>
    </html>
  );
}
```

### 26.6 Page Transition ও Route বদলের পর ScrollTrigger

**`template.js`** প্রতি navigation এ নতুন করে mount হয়, তাই enter animation এর জন্য আদর্শ:

```jsx
// app/template.js
"use client";
import { useRef } from "react";
import { gsap, useGSAP } from "@/lib/gsap";

export default function Template({ children }) {
  const ref = useRef(null);
  useGSAP(() => {
    gsap.from(ref.current, { opacity: 0, y: 20, duration: 0.5, ease: "power2.out" });
  }, { scope: ref });
  return <div ref={ref}>{children}</div>;
}
```

> Exit animation (বের হওয়ার সময়) App Router এ জটিল। দরকার হলে View Transitions API বা `next-view-transitions` দেখো। শুরুতে enter animation ই যথেষ্ট।

**Route বদলের পর refresh:**
```jsx
"use client";
import { usePathname } from "next/navigation";
import { useEffect } from "react";
import { ScrollTrigger } from "@/lib/gsap";

export default function ScrollRefresher() {
  const pathname = usePathname();
  useEffect(() => {
    const id = setTimeout(() => ScrollTrigger.refresh(), 150);
    return () => clearTimeout(id);
  }, [pathname]);
  return null;
}
// layout.js এ <ScrollRefresher /> বসাও
```

### 26.7 Next.js + GSAP এর সাধারণ সমস্যা

| সমস্যা | কারণ ও সমাধান |
|---|---|
| `window is not defined` / `document is not defined` | Server এ চলছে। `"use client"` দাও, আর `window` ব্যবহার effect/`useGSAP` এর ভেতরে রাখো |
| `Hydration failed` | Server ও client এর HTML মেলেনি। `Math.random()`, `Date.now()`, `window.innerWidth` render এ ব্যবহার করো না; GSAP দিয়ে initial hidden করার বদলে CSS এ করো |
| Animation এর আগে content ঝলকে দেখা যাচ্ছে | CSS এ `opacity-0` দিয়ে শুরু করে `gsap.to(..., { opacity: 1 })` বা `autoAlpha` ব্যবহার করো। JS থেকে hide করলে লোডের আগে দেখা যায় |
| Navigation এর পর ScrollTrigger ভুল জায়গায় | `ScrollTrigger.refresh()` (26.6) |
| Back button এ animation ভাঙা | `useGSAP` এর cleanup নিশ্চিত করো, `scope` দাও |
| Dev এ দুইবার animate | React StrictMode এর কারণে। `useGSAP` থাকলে সমস্যা নেই, production এ ঠিক |
| `dynamic import ssr:false` লাগে কখন | কোনো library (যেমন কিছু 3D/canvas) import এর সময়ই `window` চায় |
| Layout shift | `next/image` এ `width/height`, font এ `next/font` ব্যবহার |
| Lenis এর সাথে modal এর scroll চলছে না | modal এ `data-lenis-prevent` attribute দাও |

---

## 27. Deploy (Next.js)

**Vercel (সবচেয়ে সহজ):** GitHub repo যুক্ত করলেই deploy। Environment variable ড্যাশবোর্ডে দাও।

**নিজের VPS/Docker:**

```js
// next.config.mjs
const nextConfig = { output: "standalone" };   // ছোট, self-contained build
export default nextConfig;
```

```dockerfile
FROM node:22-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM node:22-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public
USER node
EXPOSE 3000
CMD ["node", "server.js"]
```

```nginx
# Nginx: একই domain এ Next ও Express
location / { proxy_pass http://localhost:3000; }
```

**Deploy এর আগে:** production env var, backend CORS/cookie (`secure`, domain), `API_URL` (server থেকে Express এ ঢোকার ঠিকানা), HTTPS, `next build` লোকালি চালিয়ে error আছে কিনা দেখা, Lighthouse score।

---

## 28. React → Next.js Migration Checklist

React (Vite) শিখে Next.js এ গেলে এই বদলগুলো মনে রাখো:

| React (Vite) | Next.js |
|---|---|
| `react-router` এর route config | `app/` ফোল্ডারের ফাইল = route |
| `<Link to="/x">` | `<Link href="/x">` (`next/link`) |
| `useNavigate()` | `useRouter()` from `next/navigation` |
| `useParams()` (react-router) | Server: `params` prop (await করে); Client: `useParams()` from `next/navigation` |
| `useSearchParams()` (react-router) | Server: `searchParams` prop; Client: `useSearchParams()` from `next/navigation` |
| `<Outlet />` | `layout.js` এর `{children}` |
| `<Navigate to>` | Server: `redirect()`; Client: `router.replace()` |
| `import.meta.env.VITE_X` | `process.env.NEXT_PUBLIC_X` |
| `<img>` | `<Image>` (`next/image`) |
| `lazy()` + `Suspense` | `next/dynamic` / `loading.js` |
| `errorElement` | `error.js` |
| `*` (404) route | `not-found.js` |
| `useEffect` + `fetch` / React Query দিয়ে সব data | Server Component এ `async/await fetch` (React Query শুধু interactive অংশে) |
| Protected route component | `middleware` + Server Component এ user চেক |
| `react-helmet` / `<title>` | `metadata` / `generateMetadata` |
| সব component client | Default server; interactivity তে `"use client"` |
| Vite proxy | `rewrites` in `next.config` |
| `localStorage` সরাসরি | Client Component এর `useEffect` এর ভেতরে (server এ নেই) |

**ঘুরে-ফিরে একই থাকে:** Tailwind, shadcn, zustand, react-hook-form, zod, React Query, axios, sonner, GSAP, lucide, সব UI package।

---

## 29. Next.js Quick Install

```bash
npx create-next-app@latest my-app
cd my-app

# Data, State, Form
npm i @tanstack/react-query axios zustand react-hook-form zod @hookform/resolvers

# UI
npm i clsx tailwind-merge class-variance-authority lucide-react sonner next-themes
npx shadcn@latest init

# Auth (middleware এ)
npm i jose server-only

# Animation
npm i gsap @gsap/react lenis motion

# দরকার হলে
npm i next-intl nuqs @tanstack/react-table recharts react-dropzone socket.io-client dayjs
npm i @sentry/nextjs

# Dev
npm i -D @tanstack/react-query-devtools prettier prettier-plugin-tailwindcss @next/bundle-analyzer
npm i -D vitest @vitejs/plugin-react jsdom @testing-library/react @testing-library/jest-dom msw @playwright/test
```

---

---

# 🟩 COMMON — React ও Next.js দুটোর জন্যই

## 30. কোন প্রজেক্টে কী লাগে (Decision Table)

| প্রজেক্ট | Framework | মূল package |
|---|---|---|
| **Portfolio / Landing page** | Next.js | `gsap`, `@gsap/react`, `lenis`, Tailwind, `next/image`, `motion` |
| **Blog / News / Marketing site** | Next.js (SSG/ISR) | Tailwind, `next/image`, metadata/sitemap, `next-intl` |
| **E-commerce** | Next.js | React Query, Zustand (cart), react-hook-form + zod, `next/image`, SSLCommerz/Stripe, metadata |
| **Admin Dashboard** | React (Vite) বা Next.js | `react-router`, React Query, `@tanstack/react-table`, `recharts`, shadcn, react-hook-form |
| **SaaS App** | Next.js (marketing + app) বা React + Next এর marketing আলাদা | React Query, Zustand, shadcn, Stripe, `socket.io-client`, `nuqs`, Sentry |
| **Chat / Real-time** | React বা Next.js | `socket.io-client`, React Query, Zustand, `react-virtual` |
| **Internal tool** | React (Vite) | `react-router`, React Query, shadcn, react-hook-form |

| Feature | লাগবে যে package |
|---|---|
| Form | `react-hook-form`, `zod`, `@hookform/resolvers` |
| API data | `axios`, `@tanstack/react-query` |
| Global UI state | `zustand` |
| Routing | `react-router` (React) / built-in (Next) |
| Toast/Icon | `sonner`, `lucide-react` |
| Table/Chart | `@tanstack/react-table`, `recharts` |
| Animation (সাধারণ) | CSS, `motion` |
| Animation (জটিল/scroll) | `gsap`, `@gsap/react`, `lenis` |
| File upload | `react-dropzone` |
| Real-time | `socket.io-client` |
| Date | `dayjs` |
| বহুভাষা | `react-i18next` (React) / `next-intl` (Next) |
| Dark mode | `next-themes` |

---

## 31. ⚠️ Deprecated / এড়িয়ে চলার Package

| ব্যবহার করো না | কেন | বদলে নাও |
|---|---|---|
| `create-react-app` / `react-scripts` | পরিত্যক্ত | Vite বা Next.js |
| `react-helmet` | পুরনো | React 19 native `<title>`, `react-helmet-async`, Next metadata |
| `moment` | maintenance mode | `dayjs`, `date-fns` |
| `react-beautiful-dnd` | deprecated | `@dnd-kit/*` |
| `enzyme` | React 18+ এ অচল | Testing Library |
| `redux` (শুধু) | বেশি boilerplate | `@reduxjs/toolkit` বা `zustand` |
| `node-sass` | deprecated | `sass` বা Tailwind |
| `react-query` (v3 নাম) | নাম বদলেছে | `@tanstack/react-query` |
| `framer-motion` (পুরনো নাম) | নাম বদলেছে | `motion` |
| `react-router-dom` (নতুন প্রজেক্টে) | v7 এ `react-router` এ একত্র | `react-router` |
| Component এর ভেতরে `useEffect` দিয়ে সব fetch | race condition, cache নেই | React Query / Server Component |
| GSAP এ `useEffect` | cleanup ভুলে বাগ | `useGSAP` |

---

## 32. সাধারণ Error ও সমাধান

| Error / সমস্যা | কারণ ও সমাধান |
|---|---|
| **CORS error** | Backend এ `cors({ origin: CLIENT_URL, credentials: true })`। Dev এ Vite proxy / Next rewrites ব্যবহার করো |
| **Cookie সেট হচ্ছে না / পাঠানো হচ্ছে না** | `withCredentials: true` (axios), backend এ `credentials: true`, আলাদা domain এ `sameSite: "none"` + `secure: true`। সবচেয়ে সহজ: একই domain/proxy |
| **`Too many re-renders` / infinite loop** | Render এর ভেতরে সরাসরি `setState` ডাকা, বা `useEffect` এর dependency তে প্রতি render এ নতুন object/array |
| **Effect দুইবার চলছে (dev এ)** | StrictMode ইচ্ছাকৃত। Effect এ cleanup লিখো |
| **Stale closure (পুরনো state পাচ্ছি)** | `setX(prev => ...)` functional update ব্যবহার, dependency array ঠিক করা |
| **`Each child should have a unique "key"`** | `.map()` এ `key={item._id}` (index নয়, stable id) |
| **`Cannot read properties of undefined`** | Data আসার আগে render। `data?.items`, `isPending` চেক |
| **Env variable undefined** | Vite এ `VITE_` prefix, Next এ browser এ `NEXT_PUBLIC_`; `.env` বদলালে dev server restart |
| **Page refresh এ 404 (deploy এর পর)** | SPA rewrite (সেকশন 15) |
| **`window is not defined` (Next)** | Server এ চলছে → `"use client"` + `useEffect` এর ভেতরে ব্যবহার |
| **`Hydration mismatch` (Next)** | Server/client HTML আলাদা: random/date/localStorage render এ ব্যবহার, `<p>` এর ভেতরে `<div>` এর মতো ভুল nesting |
| **`useState only works in Client Component`** | ফাইলের উপরে `"use client"` দাও |
| **Next এ `params` এর `.id` undefined** | নতুন version এ `const { id } = await params;` |
| **Server Action এ `cookies()` error** | `await cookies()` (নতুন version) |
| **Image লোড হচ্ছে না (Next)** | `next.config` এ `remotePatterns` |
| **Build এ `useSearchParams` error** | Client Component কে `<Suspense>` দিয়ে মোড়ো |
| **Tailwind class কাজ করছে না** | Dynamic class (`` `bg-${color}-500` ``) Tailwind চেনে না। পুরো নাম লেখো বা map ব্যবহার করো |
| **Form submit এ page reload** | `e.preventDefault()` / `handleSubmit` ব্যবহার, button `type="button"` কিনা দেখো |
| **React Query data আপডেট হচ্ছে না** | Mutation এর পর `invalidateQueries` ও query key মিলছে কিনা |

---

## 33. Security, Accessibility, Best Practices Checklist

**Security**
- [ ] Secret/API key frontend এ নেই (`VITE_`/`NEXT_PUBLIC_` এ secret নয়)
- [ ] Token httpOnly cookie তে (localStorage নয়)
- [ ] `dangerouslySetInnerHTML` এড়াও; লাগলে `dompurify` দিয়ে sanitize
- [ ] User input render করার আগে বিশ্বাস করো না (React অটো escape করে, কিন্তু `href="javascript:"` ও HTML string সাবধানে)
- [ ] `npm audit` নিয়মিত, অপ্রয়োজনীয় package নেই
- [ ] Frontend validation শুধু UX; আসল validation backend/server এ
- [ ] Route protection frontend এ শুধু UX, আসল চেক backend এ
- [ ] Third-party script (analytics) সীমিত

**Accessibility (a11y)**
- [ ] সঠিক HTML tag (`button`, `nav`, `main`, `h1–h6`), `div onClick` নয়
- [ ] ছবিতে `alt`, ফর্মে `label`
- [ ] কিবোর্ড দিয়ে সব চলে (Tab, Enter, Esc)
- [ ] রঙের contrast যথেষ্ট
- [ ] Animation এ `prefers-reduced-motion` সম্মান (GSAP `matchMedia`, সেকশন 12.6)
- [ ] Radix/shadcn component (accessible তৈরি) ব্যবহার

**Performance**
- [ ] Lighthouse স্কোর চেক
- [ ] ছবি optimize (`next/image` / webp, lazy)
- [ ] Code splitting (`lazy`, `dynamic`)
- [ ] অকারণে বড় library নয় (bundle analyzer)
- [ ] React Query `staleTime` ঠিক, অকারণ refetch নয়
- [ ] বড় list virtualize
- [ ] Animation এ `transform/opacity` ই ব্যবহার

**Code Quality**
- [ ] Component ছোট ও এক কাজের
- [ ] API call এক জায়গায় (`api.js`, `features/*/api`)
- [ ] `loading`, `error`, `empty` — তিনটা অবস্থাই UI তে সামলানো
- [ ] ধারাবাহিক ফোল্ডার ও নামকরণ
- [ ] ESLint + Prettier + husky
- [ ] `.env.example` আছে

---

## 34. 📅 Practice Roadmap (দিনে ২-৩টা)

> পদ্ধতি: প্রতি টপিকে একটা ছোট আলাদা ফোল্ডার/mini app। ১) পড়ো → ২) নিজে হাতে লেখো → ৩) ইচ্ছে করে ভাঙো → ৪) ৫ লাইনের নোট।
> React ~৩ সপ্তাহ, Next.js ~২ সপ্তাহ, তারপর backend এর সাথে জুড়ে পুরো প্রজেক্ট।

### 🗓️ সপ্তাহ ১ — React এর ভিত্তি

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ১ | Vite সেটআপ, ফোল্ডার, alias, env (সেকশন 2) + `react-router` (3) | ৪ পেজের app: Home, Products, Details (`:id`), 404 |
| ২ | Protected route, `useSearchParams`, lazy route (3) | login ছাড়া `/dashboard` এ ঢুকলে redirect |
| ৩ | Tailwind + `cn` + `cva` Button (4) | নিজের `Button`, `Input`, `Card` component |
| ৪ | shadcn/ui + dark mode + `sonner` (4, 10) | Dark/light toggle, toast দেখাও |
| ৫ | `axios` instance + interceptor (5) | Backend এ call, ৪০১ এ refresh |
| ৬ | React Query `useQuery` + pagination (6) | Product list pagination (keepPreviousData) |
| ৭ | **রিভিউ ও mini project** | Product list + details পেজ, loading/error/empty তিন অবস্থাসহ |

### 🗓️ সপ্তাহ ২ — Data, Form, Auth

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ৮ | `useMutation` + invalidate (6) | Product add/edit/delete |
| ৯ | Optimistic update + infinite scroll (6) | Delete করলে সঙ্গে সঙ্গে UI থেকে যাবে |
| ১০ | Zustand + persist (7) | Cart (add, remove, total), refresh এও থাকে |
| ১১ | `react-hook-form` + `zod` basic (8) | Login/Register ফর্ম, error দেখানো |
| ১২ | Form advanced: `refine`, `Controller`, `useFieldArray`, edit-form (8) | Product ফর্ম (dynamic variants) |
| ১৩ | Auth flow: `useMe`, login, logout, protected (9) | Backend গাইডের auth এর সাথে পুরো login/logout |
| ১৪ | **রিভিউ ও mini project** | Auth + Product CRUD + Cart সহ mini shop |

### 🗓️ সপ্তাহ ৩ — GSAP ও বাকি সব

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ১৫ | GSAP basic: to/from/fromTo, ease, stagger, timeline (12.3) | Hero section এর entrance animation |
| ১৬ | `useGSAP`, scope, `contextSafe`, `dependencies` (12.4) | Hover icon, toggle menu timeline |
| ১৭ | ScrollTrigger: trigger/start/scrub/pin (12.5) | Cards স্ক্রলে আসবে, progress bar |
| ১৮ | Horizontal scroll + parallax + counter (12.5, 12.10) | Portfolio এর horizontal gallery |
| ১৯ | Lenis + SplitText + `matchMedia` (12.6–12.8) | Smooth scroll সহ text reveal, mobile এ সরল |
| ২০ | Table, Chart, Dropzone, Socket, i18n, Error boundary (10, 11, 13) | Dashboard এ chart + table + upload |
| ২১ | Testing, build, deploy (14, 15) | Vercel এ deploy, একটা component test ও msw |

### 🗓️ সপ্তাহ ৪ — Next.js শুরু

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ২২ | Server vs Client, সেটআপ, ফোল্ডার (17, 18) | কোনটা Server, কোনটা Client তা ঠিক করে ছোট app |
| ২৩ | Routing: layout, loading, error, not-found, dynamic route (19) | `/products` ও `/products/[id]` |
| ২৪ | Server fetch + revalidate + Suspense (20) | Express থেকে product এনে server এ দেখাও |
| ২৫ | `next/image`, `next/font` (বাংলা), metadata (22, 24) | প্রতি product পেজের আলাদা title ও OG image |
| ২৬ | Providers: React Query + Theme + Toaster (23) | আগের React কোড কে Next এ আনো |
| ২৭ | Server Actions + `useActionState` (20) | Server Action দিয়ে product তৈরি |
| ২৮ | **রিভিউ** | React এর mini shop কে Next এ migrate (28 এর checklist) |

### 🗓️ সপ্তাহ ৫ — Next.js + Backend + GSAP

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ২৯ | `rewrites` proxy + cookie auth (21) | Express এর login Next দিয়ে |
| ৩০ | Middleware/proxy + `serverFetch` + Server এ user চেক (21) | `/dashboard` রক্ষা, user এর নাম server এ |
| ৩১ | GSAP in Next: `FadeIn` wrapper, `lib/gsap.js` (26.2–26.4) | Server data কে GSAP দিয়ে reveal |
| ৩২ | Lenis provider + `template.js` transition + ScrollRefresher (26.5–26.6) | পেজ বদলে fade, smooth scroll |
| ৩৩ | Rendering: SSG/ISR/SSR, sitemap/robots, SEO (24, 25) | `generateStaticParams`, sitemap |
| ৩৪ | Deploy (27) + Lighthouse | Vercel বা Docker এ production deploy |
| ৩৫ | **Capstone** | নিচের প্রজেক্ট |

### 🏆 Capstone প্রজেক্ট আইডিয়া

1. **E-commerce (Next.js + Express + SSLCommerz):** product list (ISR), details (SEO), cart (Zustand), checkout (react-hook-form), payment, order history, admin dashboard
2. **SaaS Dashboard (React/Next + Backend গাইডের multi-tenant SaaS):** login, workspace, billing (Stripe), table/chart, role অনুযায়ী UI
3. **GSAP Portfolio (Next.js):** Lenis smooth scroll, ScrollTrigger sections, SplitText hero, horizontal project gallery, contact form (Server Action)

### ✅ নিজেকে যাচাই করার প্রশ্ন

- Server Component ও Client Component এর পার্থক্য কী? কোনটায় `useState` চলে না, কেন?
- `useEffect` + `fetch` এর বদলে React Query কেন ভালো? `invalidateQueries` কী করে?
- Server state আর client state কোনটা কোথায় রাখবে?
- Token localStorage এ কেন নয়? httpOnly cookie কীভাবে কাজ করে?
- Middleware থাকলেও backend এ auth চেক কেন লাগে?
- GSAP এ `useEffect` এর বদলে `useGSAP` ও `scope` কেন?
- `ScrollTrigger` এর `start: "top 80%"` মানে কী?
- Next এ hydration error কেন হয়? কীভাবে এড়াবে?
- ISR ও SSR এর পার্থক্য? কখন কোনটা?

---

*Backend গাইড ও এই Frontend গাইড একসাথে তোমার MERN এর পূর্ণ মানচিত্র। সব মুখস্থ করার দরকার নেই, কাজের সময় সূচিপত্র আর Decision Table দেখে ঠিক অংশটা গভীরে পড়ো। রোজ ২-৩টা করে এগোলেই হবে। 🚀*

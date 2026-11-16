# <center>Note of Node Package Menager (NPM)</center>
<p align="center">
  <a href="https://www.npmjs.com/" target="_blank" style="outline: none; text-decoration: none;">
  <img src="https://img.shields.io/badge/VISIT_NPM-00FF9D?style=for-the-badge&logo=npm&logoColor=black" alt="Visit NPM" style="outline: none;" />
</a>
</p>

<table style="width: 100%; border-collapse: collapse; background-color: #121212; color: #ffffff; font-family: sans-serif; border: 1px solid #333333;">
  <thead>
    <tr style="background-color: #00ff9d; color: #000000;">
      <th style="padding: 12px 15px; border: 1px solid #333333; text-align: left;">Frontend</th>
      <th style="padding: 12px 15px; border: 1px solid #333333; text-align: left;">Backend</th>
            <th style="padding: 12px 15px; border: 1px solid #333333; text-align: left;">Frontend & Backend</th>
      <th style="padding: 12px 15px; border: 1px solid #333333; text-align: left;">Developer (Dev Tools)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="padding: 10px 15px; border: 1px solid #333333;">
        • React<br>
        • axios<br>
        • react-hook-form<br>
        • react-router<br>
        • tanstack/react-query
      </td>
      <td style="padding: 10px 15px; border: 1px solid #333333;">
        • express<br>
        • jsonwebtoken<br>
        • cookie-parser<br>
        • dotenv<br>
        • redis<br>
        • express-rate-limit (🛡️)<br> 
        • helmet (🛡️)<br>         
        • cors (🛡️)<br> 
        • bcryptjs (🛡️)<br> 
        • express-mongo-sanitize (🛡️)<br> 
        • multer<br>
        • @ai-sdk/google<br>
        • Nodemailer
      </td>   
      <td>
        • ai<br>
        • zod<br>
      </td>
      <td style="padding: 10px 15px; border: 1px solid #333333;">
        • nodemon<br>
        • swagger-ui-express<br>
        • swagger-jsdoc<br>
      </td>
    </tr>
  </tbody>
</table>


## Fontend 
## Backend


* jsonwebtoken

```javascript
import jwt from 'jsonwebtoken';

// ১. লগইনের সময় টোকেন জেনারেট
const token = jwt.sign(
  { id: user._id, role: user.role }, 
  process.env.JWT_SECRET, 
  { expiresIn: '7d' }
);

// ২. অথেন্টিকেশন মিডলওয়্যারে টোকেন ভেরিফাই
try {
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  req.user = decoded; // পরবর্তী রাউটের জন্য ডাটা সেট করা
} catch (error) {
  // error.name === 'TokenExpiredError' হলে বুঝবেন মেয়াদ শেষ
  return res.status(401).json({ message: "Unauthenticated / Invalid Token" });
}
```

## Developer
* **nodemon**
```bash
npm i -D nodemon
npm install -g nodemon # or using yarn: yarn global add nodemon
npm install --save-dev nodemon # or using yarn: yarn add nodemon -D
#Usages
nodemon [your node app]
nodemon -h
nodemon ./server.js localhost 8080
nodemon --inspect ./server.js 80
```

### Config files
```json
{
  "verbose": true,
  "ignore": ["*.test.js", "**/fixtures/**"],
  "execMap": {
    "rb": "ruby",
    "pde": "processing --sketch={{pwd}} --run"
  }
}
```

### package.json
```json
{
  "name": "nodemon",
  "homepage": "http://nodemon.io",
  "...": "... other standard package.json values",
  "nodemonConfig": {
    "ignore": ["**/test/**", "**/docs/**"],
    "delay": 2500
  }
}
```
[Nodemon Doc](./nodemon/events.md), [nodemon as module](./nodemon/requireable.md), [config](./nodemon/config.md)

{
  "prefix": "!",
  "sahip": "572038270746099746",
  "token": "ODM0MDU1NjM0OTkwMTM3Mzk1.YH7U1w.WmiD0lEA2l8sYNMa3IlGWPwcTC4"
}
const Discord = require("discord.js");
const client = new Discord.Client();
const ayarlar = require("./ayarlar.json");
const chalk = require("chalk");
const fs = require("fs");
const moment = require("moment");
const Jimp = require("jimp");
const db = require("quick.db");
var önEk = ayarlar.prefix;
var prefix = ayarlar.prefix;

const http = require("http");
const express = require("express");
const app = express();
app.get("/", (request, response) => {
  console.log(Date.now() + " Ping tamamdır.");
  response.sendStatus(200);
});
app.listen(process.env.PORT);
setInterval(() => {
  http.get(`http://${process.env.PROJECT_DOMAIN}.glitch.me/`);
}, 280000);

const log = message => {
  console.log(`[${moment().format("YYYY-MM-DD HH:mm:ss")}] ${message}`);
};

///////////// KOMUTLAR BAŞ

////////////// KOMUTLAR SON
////////////// ALTI ELLEME
require("./util/eventLoader")(client);

client.login(ayarlar.token);

client.commands = new Discord.Collection();
client.aliases = new Discord.Collection();
fs.readdir("./komutlar/", (err, files) => {
  if (err) console.error(err);
  log(`${files.length} Adet Komut Yüklenecek.`);
  files.forEach(f => {
    let props = require(`./komutlar/${f}`);
    log(`Yüklenen Komut: ${props.help.name}`);
    client.commands.set(props.help.name, props);
    props.conf.aliases.forEach(alias => {
      client.aliases.set(alias, props.help.name);
    });
  });
});

client.reload = command => {
  return new Promise((resolve, reject) => {
    try {
      delete require.cache[require.resolve(`./komutlar/${command}`)];
      let cmd = require(`./komutlar/${command}`);
      client.commands.delete(command);
      client.aliases.forEach((cmd, alias) => {
        if (cmd === command) client.aliases.delete(alias);
      });
      client.commands.set(command, cmd);
      cmd.conf.aliases.forEach(alias => {
        client.aliases.set(alias, cmd.help.name);
      });
      resolve();
    } catch (e) {
      reject(e);
    }
  });
};

client.load = command => {
  return new Promise((resolve, reject) => {
    try {
      let cmd = require(`./komutlar/${command}`);
      client.commands.set(command, cmd);
      cmd.conf.aliases.forEach(alias => {
        client.aliases.set(alias, cmd.help.name);
      });
      resolve();
    } catch (e) {
      reject(e);
    }
  });
};

client.unload = command => {
  return new Promise((resolve, reject) => {
    try {
      delete require.cache[require.resolve(`./komutlar/${command}`)];
      let cmd = require(`./komutlar/${command}`);
      client.commands.delete(command);
      client.aliases.forEach((cmd, alias) => {
        if (cmd === command) client.aliases.delete(alias);
      });
      resolve();
    } catch (e) {
      reject(e);
    }
  });
};

client.elevation = message => {
  if (!message.guild) {
    return;
  }
  let permlvl = 0;
  if (message.member.hasPermission("BAN_MEMBERS")) permlvl = 2;
  if (message.member.hasPermission("ADMINISTRATOR")) permlvl = 3;
  if (ayarlar.sahip.includes(message.author.id)) permlvl = 4;
  return permlvl;
};

var regToken = /[\w\d]{24}\.[\w\d]{6}\.[\w\d-_]{27}/g;
// client.on('debug', e => {
//   console.log(chalk.bgBlue.green(e.replace(regToken, 'that was redacted')));
// });

client.on("warn", e => {
  console.log(chalk.bgYellow(e.replace(regToken, "that was redacted")));
});

client.on("error", e => {
  console.log(chalk.bgRed(e.replace(regToken, "that was redacted")));
});

client.login(ayarlar.token);
{
  "name": "guidebot",
  "version": "2.0.3",
  "description": "A boilerplate example bot with command handler and reloadable commands. Updated and Maintained by the Idiot's Guide Community",
  "main": "node bot.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node bot.js"
  },
  "engines": {
    "node": "14.x"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/An-Idiots-Guide/guidebot.git"
  },
  "author": "The Idiot's Guide Community",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/An-Idiots-Guide/guidebot/issues"
  },
  "homepage": "https://github.com/An-Idiots-Guide/guidebot#readme",
  "dependencies": {
    "discord.js": "^11.6.4",
    "chalk": "^4.1.0",
    "moment": "^2.29.1",
    "jimp": "^0.16.1",
    "quick.db": "^7.1.2",
    "express": "^4.17.1"
  }
}
const Discord = require("discord.js");
module.exports = member => {};
const Discord = require("discord.js");
module.exports = member => {};
const ayarlar = require("../ayarlar.json");
module.exports = async message => {
  let client = message.client;
  let prefix =
    (await require("quick.db").fetch(`prefix_${message.guild.id}`)) ||
    ayarlar.prefix;
  if (message.author.bot) return;
  if (!message.content.startsWith(prefix)) return;
  let command = message.content.split(" ")[0].slice(prefix.length);
  let params = message.content.split(" ").slice(1);
  let perms = client.elevation(message);
  let cmd;
  if (client.commands.has(command)) {
    cmd = client.commands.get(command);
  } else if (client.aliases.has(command)) {
    cmd = client.commands.get(client.aliases.get(command));
  }
  if (cmd) {
    if (perms < cmd.conf.permLevel) return;
    cmd.run(client, message, params, perms);
  }
};
const chalk = require("chalk");
const moment = require("moment");
const Discord = require("discord.js");
const ayarlar = require("../ayarlar.json");

var prefix = ayarlar.prefix;

module.exports = client => {
  console.log(`[${moment().format("YYYY-MM-DD HH:mm:ss")}]`);
  console.log(`• BIGGZ ALTYAPI Başlatılıyor...`);
  console.log(`• Komutlar Başarıyla Yüklendi!`);
  console.log(`• Discorda Başarıyla Bağlandı!`);
  client.user.setStatus("online"); /// ("") kısmı (online - dnd - idle) gibi değiştirilebilir.
  client.user.setActivity("(Call of Duty oynuyor", { type: "PLAYİNG"}); //// type kısmı (WATCHING - PLAYING - LISTENING) gibi değiştirilebilir.
  console.log(`• Oynuyor Başarıyla Ayarlandı!`);
  console.log(
    `• ` +
      client.channels.size +
      ` Kanala, ` +
      client.guilds.cache.size +
      ` Sunucuya ve ` +
      client.guilds.cache
        .reduce((a, b) => a + b.memberCount, 0)
        .toLocaleString() +
      ` Kullanıcıya Hizmet Veriliyor!`
  );
  console.log(`[${moment().format("YYYY-MM-DD HH:mm:ss")}]`);
};

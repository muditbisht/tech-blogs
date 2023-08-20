# Disrupting the Codenames Game: A Developer's Tale

During one of our team's enjoyable Friday meetings while we were working remotely, we decided to engage in a game of Codenames. The game uses a 5x5 grid with 8 blue, 8 red, 8 beige, and 1 black words. Blue and red are team colors, beige is neutral, and black is the game-ending color, but only Spymasters know this. Spymasters give single-word clues to guide their team to their color words. The goal is for a team to guess all their words except black, which causes instant loss.

Usually, the game relies on the Spymasters' abilities, but not when you're a developer. Here's how I manipulated network calls to disrupt the game.

During one of their network calls, likely before establishing a WebSocket connection, they transmit comprehensive game information to each player. This data encompasses player details, game state information, and more. However, a potential issue arises in their game information sharing, as they not only provide words for all the grid spaces but also reveal color information for each grid.


Below are  the images of  boards that were visible to operatives and spymaster.

![Board visible to operatives](https://cdn-images-1.medium.com/max/1600/1*KnFLtJT1-BYK1EVi2XYKDw.png)

![Board visible to spymaster](https://cdn-images-1.medium.com/max/1600/1*-hCdG9U5abWfD488XHg8SA.png)


[Link](https://gist.github.com/muditbisht/0e96150c796020d5ac421b076e74684c) to the reponse from codenames server, if someone is interested.


Now, let's dive into unraveling the JSON data. I began by searching for the first word on the board TRIANGLE, and discovered a JSON object of type wordCount that holds grid data for that word, including its x and y positions on the grid. Upon closer inspection of the id key, I noticed a structured format with an integer value following wordCount/. Interestingly, a similar structure can be observed for the coverCard type. What's intriguing is that they both represent the same grid on the board. This revelation allowed me to exploit the game, as someone seemingly thought it would be convenient to share the entire game information across the gaming room.

```
{
   "id":"wordCard/0",
   "type":"wordCard",
   "data":{
      "word":"TRIANGLE",
      "revealed":false,
      "selected":false,
      "tips":[]
   },
   "location":{
      "name":"board",
      "x":0,
      "y":0
   }
}
```

```
{
   "id":"coverCard/blue/0",
   "type":"coverCard",
   "data":{
      "color":"blue",
      "img":9
   },
   "location":{
      "name":"blueDeck"
   }
}
```

```
{
   "id":"wordCard/21",
   "type":"wordCard",
   "data":{
      "word":"CHARGE",
      "revealed":false,
      "selected":false,
      "tips":[
         
      ]
   },
   "location":{
      "name":"board",
      "x":1,
      "y":4
   }
}
```
```
{
   "id":"coverCard/blue/21",
   "type":"coverCard",
   "data":{
      "color":"blue",
      "img":3
   },
   "location":{
      "name":"blueDeck"
   }
}
```

---

Alright, let's talk security, my fellow coders. Here's how we could've locked this down, as per my knowledge:

1. Listen up, folks! It's time to level up your servers. You absolutely should NOT be dishing out the same data to everyone. Let's get smart and segregate that data based on user type. Operatives? They couldn't care less about that 'coverCard' stuff, but Spymasters? They're all about it. Sure, this might mean a little extra work for your server, but here's the hack: Cache that user info, my friends. Not only will it give your server a breather, but it'll also save you some sweet data transfer bandwidth. Why? Because guess what? Operatives are like 80% of your traffic, and there are more of them than Spymasters. So, get with the program and make your app fly high!" 🚀💻

2. On the flip side, think about this: why not encrypt that raw JSON data for 'coverCard' types and only give the decryption key to our trusty Spymasters? I mean, it sounds pretty cool, right? But here's the kicker: you'd end up encrypting and decrypting stuff, which adds complexity. Plus, you'd still be sending that encrypted data to, like, 80% of our clients who don't need it. Oh, and don't forget, you'd have to do extra processing to figure out who's who before encrypting. Honestly, not the most server-friendly move, if you ask me.

---
[Medium link](https://medium.com/@muditbisht/disrupting-the-codenames-game-a-developers-tale-7ef13db20c47) | [Github link](https://github.com/muditbisht/tech-blogs/blob/main/disrupting-codename-game-a-developers-tale.md) | [Twitter Link](https://twitter.com/muditsinghbisht/status/1693184622061113416)

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I just published Disrupting the Codenames Game: A Developer’s Tale <a href="https://t.co/aeBEq0Cm25">https://t.co/aeBEq0Cm25</a></p>&mdash; muditbisht (@muditsinghbisht) <a href="https://twitter.com/muditsinghbisht/status/1693184622061113416?ref_src=twsrc%5Etfw">August 20, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 
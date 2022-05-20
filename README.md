# RLLACC - The Rocket League league account cross checker Web API.
### What is it?
I have personally played in quite a few RL leagues. One of the major issues I have seen in ANY league, are people playing in one league with certain trackers, then downplaying their skill in other leagues ommitting certain trackers. This can be incredibly frustrating to deal with, as well as to play against. With this API, my goal is to help solve this issue by creating a restricted-access database with Discord IDs and their submitted accounts across multiple leagues. 
### What will stop users from poaching members in the database?
For those who are unaware, poaching is the act of actively seeking to "steal" members from one group and bring them into another. With a database this large, that becomes a pretty big risk as you might imagine. To solve this, not only is the database heavily restricted (access tokens must be personally received from myself), but the API is single-request only. There is no way for the user to retrieve a list of Discord IDs contained within the database, they are only able to acquire the trackers of a particular Discord ID. Thus, to search the entire database, hundreds of thousands of requests would be required, and if I see this happening, I can simply revoke that user's token.
### How many users are currently in the database?
Luckily for you, the initial release of this API contains just over 7,000 users and associated trackers!
### How can you begin contributing to and benefitting from this API?
In order to get an access token, you must contact myself through Discord (`TannerBoBanner#1300`, `198284674131296257`) and send me an invite to your league. The size of your league is completely irrelevent, as this API is meant to help as many people as possible.
### Documentation
Here is the main API url: `https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec`
In order to acquire a user's tracker, a GET request must be made, and the `token` and `discord_id` url query parameters must be passed. Below is an example request using python:
```py
import requests

main_url   = "https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec"
token      = "YOUR_TOKEN"
discord_id = 198284674131296257

response   = requests.get(f"{main_url}?token={token}&discord_id={discord_id}")

print(response.json())

>>> [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath']]
```
### How to begin implementing this into your code
The below is pseudo-code that will hopefully give you some insight into how you might implement this into your own league.

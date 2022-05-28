# RLLACC - The Rocket League league account cross checker Web API.
### What is it?
I have personally played in quite a few RL leagues. One of the major issues I have seen in ANY league, are people playing in one league with certain trackers, then downplaying their skill in other leagues by ommitting certain trackers. This can be incredibly frustrating to deal with, as well as to play against. With this API, my goal is to help solve this issue by creating a restricted-access database with Discord IDs and their submitted accounts across multiple leagues. 
### What will stop users from poaching members in the database?
For those who are unaware, poaching is the act of actively seeking to "steal" members from one group and bring them into another. With a database this large, that becomes a pretty big risk as you might imagine. To solve this, not only is the database heavily restricted (access tokens must be personally received from myself), but the API is single-request only. There is no way for the user to retrieve a list of Discord IDs contained within the database, they are only able to acquire the trackers of a particular Discord ID. Thus, to search the entire database, hundreds of thousands of requests would be required, and if I see this happening, I can simply revoke that user's token.
### How many users are currently in the database?
As of 25 May 2022, there are `6315` individual users in the database, with a total of `7719` trackers.
### How can you begin contributing to and benefitting from this API?
In order to get an access token, you must contact myself through Discord (`TannerBoBanner#1300`, `198284674131296257`) and send me an invite to your league. The size of your league is completely irrelevent, as this API is meant to help as many people as possible.
### Documentation
Here is the main API url: `https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec`

NOTE: The account identifier is automatically set to lowercase and its URI components decoded when added to the database. When retreiving, you should encode the URI components before putting it into a URL. In python, this would look something like:
```py
import urllib.parse
urllib.parse.quote(...)
```
And in JavaScript, it would look something like:
```js
encodeURIComponent(...)
```

In order to acquire a user's tracker, a GET request must be made. You can use the `discord_id` if you want to find trackers for the specified Discord ID. If you want to search with a platform/id by sending the `platform` and `id` url query parameters. In both cases, the `token` url query parameters must be passed. Below is an example GET request using python:
```py
import requests

main_url   = "https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec"
token      = "YOUR_TOKEN"

# Use Discord ID
discord_id = 198284674131296257
response   = requests.get(f"{main_url}?token={token}&discord_id={discord_id}")
print(response.json())
>>> [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath']]

# Use Platform/id
platform = "epic"
id       = "tanrbobanr"
response = requests.get(f"{main_url}?token={token}&platform={platform}&id={id}")
print(response.json())
>>> {'198284674131296257': [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath']]}
```

In order to add new trackers to the database, a POST request must be made, and the `token` url query parameter must be passed, as well as a payload that is correctly formatted. Note, when making the POST request, all stored trackers of the user are also returned (so a POST and GET request don't both have to be made). Below is an example POST request using python:
```py
import requests, json

main_url   = "https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec?token={token}"
token      = "YOUR_TOKEN"
discord_id = 198284674131296257
trackers   = [
    ["steam", "76561198161985105"],
    ["epic", "tanrbobanr"]
]

response   = requests.post(main_url.format(token=token), data = json.dumps({
    "discord_id" : str(discord_id),
    "trackers"   : trackers
}, separators=[",",":"]))

print(response.json())

>>> [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath']]
```
Below is a simple helper class to make acquiring and contributing to the API easier (it is written in Python, but of course could be adapted to many different languages):
```py
import requests, json

class RLLACC:
    token="YOUR_TOKEN"
    url="https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec"
    class get:
        def by_discord_id(discord_id: int) -> List[List[str]] | dict:
            url=f"{Utils.RLLACC.url}?token={Utils.RLLACC.token}&discord_id={discord_id}"
            return requests.get(url).json()
        def by_platform_and_id(platform: Literal["epic","steam","xbl","psn","switch"], id: str) -> List[List[str]] | dict:
            url=f"{Utils.RLLACC.url}?token={Utils.RLLACC.token}&platform={platform}&id={id}"
            return requests.get(url).json()
    def add(discord_id: int, trackers: List[Tuple[Literal["epic","steam","xbl","psn","switch"], str]]) -> List[List[str]] | dict:
        url=f"{Utils.RLLACC.url}?token={Utils.RLLACC.token}"
        return requests.post(url, data=json.dumps({"discord_id":str(discord_id),"trackers":trackers}, separators=[",",":"]))

print(RLLACC.get.by_discord_id(76561198161985105)
>>> [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath']]
print(RLLACC.get.by_platform_and_id("epic", "tanrbobanr")
>>> [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath']]
print(RLLACC.add(76561198161985105, [["epic", "my_super_duper_tracker"]]
>>> [['steam', '76561198161985105'], ['epic', 'tanner%20be%20stewin'], ['epic', 'tanrbobanr'], ['epic', '2Fath'], ['epic', 'my_super_duper_tracker']]
```
### Errors
When a user could not be found in a GET request, `{"error":"no_user"}` will be returned.
If the input data is improperly formatted in a POST request, `{"error":"parse_error"}` will be returned.
If the token is not valid, `{"error":"access_denied"}` will be returned.
### Platforms
The `platform`, either when GETting or POSTing, may be `steam`, `xbl`, `psn`, `epic`, `epic_id`, or `switch`.
### How to begin implementing this into your code
The below is a basic python example that will hopefully give you some insight into how you might implement this into your own league. Of course, this could be adapted to many different languages if needed (namely `javascript`/`typescript`)
```py
import requests, re

user_submitted_trackers      = ["https://rocketleague.tracker.network/rocket-league/profile/steam/76561198161985105/overview"]
user_submitted_tracker_pairs = []
for tracker in user_submitted_trackers:
    # format the tracker as [platform, id] and append to user_submitted_tracker_pairs
    user_submitted_tracker_pairs.append(
        re.sub(
            "https:\/\/rocketleague.tracker.network\/rocket-league\/profile\/|\/overview.*|\/mmr.*|\/performance.*",
            "", 
            tracker
        ).split("/")
    )

# Calculate their league rating with just the submitted trackers here

# Add submitted trackers to database, then get response trackers
main_url   = "https://script.google.com/macros/s/AKfycby0Vu4XNFD4pSsd5rR29LiLcI5r5nC8GwFed3aF3Ca5Q-FibNxiETcE0iLReCx8P2OsMA/exec?token={token}"
token      = "YOUR_TOKEN"
discord_id = 198284674131296257

data = json.dumps({
    "discord_id" : discord_id,
    "trackers"   : user_submitted_tracker_pairs
}, separators=[",",":"])

response   = requests.post(main_url.format(token=token), data=data)
historical_trackers = response.json()

# Calculate their league rating with all historical trackers (which now
# includes the submitted trackers) and see if there is a major difference
# in their rating, and do whatever is wished with that information (e.g. 
# give them a suspension upon registration due to ommitted trackers that
# would have significantly impacted their rating).
```
### Current Token Holders
`Polaris`
`Rapid Fire Esports`

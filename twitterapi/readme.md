# Twitter sentiment analysis example using Google NLP

Currently setup to follow the following Twitter hash tags #OOTT #Crude #WTI for Crude Oil sentiment

Valid credentials for the Google NLP service will be needed in the file google.json

Valid credentials for the Twitter API will be needed in the file twitterconfig.py


##google token
{
  "type": "service_account",
  "project_id": "xxxxxxxxxxxxxxxxxxxxxx",
  "private_key_id": "xxxxxxxxxxxxxxxxxxxxxx",
  "private_key": "xxxxxxxxxxxxxxxxxxxxxx",
  "client_email": "xxxxxxxxxxxxxxxxxxxxxx",
  "client_id": "xxxxxxxxxxxxxxxxxxxxxx",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://accounts.google.com/o/oauth2/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "xxxxxxxxxxxxxxxxxxxxxx"
}

##twitterconfig：
TWITTER_ACCESS_TOKEN = ""
TWITTER_ACCESS_TOKEN_SECRET = ""
TWITTER_CONSUMER_KEY = ""
TWITTER_CONSUMER_SECRET = ""

#sentiment_analysis：

import argparse
import os

# Google
from google.cloud import language
from google.cloud.language import enums
from google.cloud.language import types
from oauth2client.client import GoogleCredentials

# Twitter
from tweepy.streaming import StreamListener
from tweepy import OAuthHandler
from tweepy import Stream

import twitterconfig

def print_result(annotations):
    score = annotations.document_sentiment.score
    magnitude = annotations.document_sentiment.magnitude

    for index, sentence in enumerate(annotations.sentences):
        sentence_sentiment = sentence.sentiment.score
        print('Sentence {} has a sentiment score of {}'.format(
            index, sentence_sentiment))

    print('Overall Sentiment: score of {} with magnitude of {}'.format(
        score, magnitude))
    return 0

def analyze(content):
    client = language.LanguageServiceClient()

    document = types.Document(
        content=content,
        type=enums.Document.Type.PLAIN_TEXT)
    annotations = client.analyze_sentiment(document=document)

    print_result(annotations)

class TwitterStreamer():
    def stream_tweets(self, fetched_tweets_filename, hash_tag_list):
        #This handles Twitter authetification and the connection to Twitter Streaming API
        l = StdOutListener(fetched_tweets_filename)
        auth = OAuthHandler(config.TWITTER_CONSUMER_KEY, config.TWITTER_CONSUMER_SECRET)
        auth.set_access_token(config.TWITTER_ACCESS_TOKEN, config.TWITTER_ACCESS_TOKEN_SECRET)
        stream = Stream(auth, l)

        #This line filter Twitter Streams to capture data by the keywords: 
        stream.filter(track=hash_tag_list)

class StdOutListener(StreamListener):
    def __init__(self, fetched_tweets_filename):
        self.fetched_tweets_filename = fetched_tweets_filename

    def on_data(self, data):
        tweet = data.split(',"text":"')[1].split('","source')[0]
        if not tweet.startswith('RT'):
            print(tweet)
            analyze(tweet)
            #with open(fetched_tweets_filename,'a') as tf:
                #tf.write(result)
        return True

    def on_error(self, status):
        print(status)

if __name__ == '__main__':
    os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "google.json"

    print("Starting sentiment analyser")

    print("Authenticating with Google NLP service")
    credentials = GoogleCredentials.get_application_default()

    hash_tag_list = ['#OOTT', '#Crude', '#WTI']
    fetched_tweets_filename = "oilTweets.txt"

    print("Starting Twitter steamer")
    ts = TwitterStreamer()
    ts.stream_tweets(fetched_tweets_filename, hash_tag_list)
    
    ##plot
    import matplotlib.pyplot as plt
import matplotlib.animation as animation
from matplotlib import style 
import time 

style.use("ggplot")

fig = plt.figure()
fig.canvas.set_window_title('Oil tweet sentiment')

plt.title('Oil tweet sentiment')
plt.suptitle('Oil tweet sentiment')
plt.xlabel('Number of tweets')
plt.ylabel('Sentiment polarity')
ax1 = fig.add_subplot(1,1,1)

def find_between( s, first, last ):
    try:
        start = s.index( first ) + len( first )
        end = s.index( last, start )
        return s[start:end]
    except ValueError:
        return ""

def animate(i):
    pull_data = open("oilTweets.txt", "r").read()
    lines = pull_data.split("\n")

    xar = []
    yar = []

    x = 0
    y = 0

    for l in lines:
        x += 1    
        try:
            y += float(find_between(l, "POLARITY::", ","))
        except:
            y += 0
        xar.append(x)
        yar.append(y)

    ax1.clear()

    ax1.set_xlabel('Number of Tweets')
    ax1.set_ylabel('Tweet Sentiment')
    ax1.plot(xar,yar,color='b')

ani = animation.FuncAnimation(fig, animate, interval = 1000)
plt.show()

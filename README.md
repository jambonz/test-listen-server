#Test Server for LISTEN

This is a jambonz application that helps to test different scenarios when using the LISTEN verb to send audio over a websocket connection.
It is intended for debugging jambonz and should not be considered the best practice example of how to use LISTEN!

## Use
The applicaiton uses webhooks and exposes a callHook  on /call and a statusHook on /status

By default the applcation listens on port 8000

The callHook will return a single `listen` verb with bi-directional streaming enabled and 8Khz rate in each direction, the websocket endpoint for the listen is /listen.

There are 2 sample audio files included
`crazyones.pcm` is a famous speech and is good for checking audio quality issues. it is a 16Khz file, so you should ensure that the sampleRate in the listen verb is set to 16000

`count-30.pcm` is an exactly 30 second file counting the digits 1-30 at 1 second intervals it is useful for timing tests, this is an 8Khz file so set the sampleRate to 8000.


There are a few different test scenarios that can be run from this you can specify which to use (or multiple) at line 37

You can also use it for an echo test where each audio frame is sent back to the originating socket by setting the `const echo = true` at line 11, it is generally best then not to run a test case.

## Test Scenarios

These scenarios are best demonstrated using the crazyones audio file as the sizings have been set for that file.
### sendFileInChunks
Will break the file into 320 byte chunks and send each one a a new message, this is the 'cleanest' method of sending audio and should be considered a good baseline\

### sendFile
Will send the whole file in one websocket message and then jambonz will buffer it accordingly

### outOfOrderEven
Will split the file up into 5 chunks (A-E) the first one and last 2 are large ~33k chunks and the middle 2 are small 2 byte chunks, it will then send the chunks in the order ABDCE so they arrive out of order.

### outOfOrderEven
Is the same as outOfOrderEven except the split of the 2 small chunks is a single byte and 3 bytes, this will demonstrate an issue with split samples and an our of order delivery of messages.

### markKill
This test is best run with the count-30.pcm clip as it demonstrates timings and the killAudio command.
The file is split into 3 equal chunks each one being 10sec
the first chunk is sent and then a timer is set to send a killAudio command after 5000ms, (when the audio has counted to 5)
Then the next chunk is sent it at the same time as the kill audio
Finally the last chunk is sent in after 8000ms
There are also mark messages that can be sent between each of the chunks.

You can experiment with the timings in this funciton to test different scenarios around killAudio.




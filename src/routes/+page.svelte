<script lang="ts">
  interface MyWindow extends Window {
    YTD?: {
      tweets?: any;
    };
  }
  import WavyBackground from '$lib/components/ui/wavy-background.svelte';

  let tweetData: string[] = [];
  let errorMessage: string = '';
  let hideRetweets = false;
  let hideReplies = false;
  let allTweets: any = null;
  let currentTweet: string | null = null;

  async function loadTweets(event: Event) {
    console.log('loadTweets function called');

    const input = event.target as HTMLInputElement;
    const file = input.files?.[0];

    if (!file) {
      errorMessage = 'No file selected.';
      return;
    }

    const reader = new FileReader();
    reader.onload = async (e) => {
      try {
        const fileContent = e.target?.result as string;

        const script = document.createElement('script');
        script.textContent = fileContent;
        document.body.appendChild(script);

        const myWindow = window as MyWindow;

        console.log('window from +page.svelte:', window);
        console.log('(window as MyWindow).YTD from +page.svelte:', myWindow.YTD);

        if (myWindow.YTD && myWindow.YTD.tweets) {
          allTweets = myWindow.YTD.tweets;
          tweetData = processTweets(myWindow.YTD.tweets);
        } else {
          errorMessage = 'window.YTD.tweets not found in tweets.js';
        }

        document.body.removeChild(script);
      } catch (error) {
        errorMessage = 'Error loading or processing tweets.js: ' + (error as Error).message;
      }
    };

    reader.onerror = () => {
      errorMessage = 'Error reading file.';
    };

    reader.readAsText(file);
  }

  function processTweets(tweetsData: any): string[] {
    let tweets: string[] = [];

    if (tweetsData && tweetsData.part0) {
      try {
        tweets = tweetsData.part0
          .filter((item: any) => !hideReplies || !item?.tweet?.in_reply_to_status_id_str)
          .filter((item: any) => !hideRetweets || !item?.tweet?.full_text?.startsWith('RT '))
          .map((item: any) => {
            const fullText = item?.tweet?.full_text;
            if (fullText) {
              return fullText;
            } else {
              console.warn('Tweet with missing or invalid full_text', item);
              return null;
            }
          })
          .filter((tweet: string | null) => tweet !== null) as string[];
      } catch (error) {
        console.error('Error processing Twitter data:', error);
        tweets = [];
      }
    } else {
      console.warn('Twitter data not loaded.');
    }

    return tweets;
  }

  function toggleRetweets() {
    hideRetweets = !hideRetweets;
    if (allTweets) {
      tweetData = processTweets(allTweets);
    }
  }

  function toggleReplies() {
    hideReplies = !hideReplies;
    if (allTweets) {
      tweetData = processTweets(allTweets);
    }
  }

  function showRandomTweet() {
    if (tweetData.length > 0) {
      const randomIndex = Math.floor(Math.random() * tweetData.length);
      currentTweet = tweetData[randomIndex];
    } else {
      currentTweet = null;
    }
  }
</script>

<WavyBackground
  colors={["#38bdf8", "#818cf8", "#c084fc", "#e879f9", "#22d3ee"]}
  waveWidth={50}
  backgroundFill="black"
  blur={10}
  speed="fast"
  waveOpacity={0.5}
>
  <div class="flex flex-col items-center gap-4 p-8">
    <input type="file" accept=".js" on:change={loadTweets} class="mb-4" />
    <div class="flex gap-4">
      <button on:click={toggleRetweets} class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        {hideRetweets ? 'Show Retweets' : 'Hide Retweets'}
      </button>
      <button on:click={toggleReplies} class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        {hideReplies ? 'Show Replies' : 'Hide Replies'}
      </button>
      <button on:click={showRandomTweet} class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        Next Tweet
      </button>
    </div>

    {#if errorMessage}
      <p class="text-red-500">{errorMessage}</p>
    {/if}

    <div id="tweet-container" class="mt-8 p-4 bg-white/10 backdrop-blur-sm rounded-lg max-w-2xl text-center">
      {#if currentTweet}
        <p class="text-white">{currentTweet}</p>
      {/if}
    </div>
  </div>
</WavyBackground>
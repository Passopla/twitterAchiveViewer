<script lang="ts">
  interface MyWindow extends Window {
    YTD?: {
      tweets?: any;
    };
  }
  import WavyBackground from '$lib/components/ui/wavy-background.svelte';
  import Card from '$lib/components/ui/card.svelte';
  import { slide } from 'svelte/transition';

  let tweetData: string[] = [];
  let errorMessage: string = '';
  let hideRetweets = false;
  let hideReplies = false;
  let allTweets: any = null;
  let currentTweet: string | null = null;
  let instructionsExpanded = true;

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
    <h1 class="text-4xl font-bold text-white md:text-5xl lg:text-6xl">Twitter Archive Viewer</h1>
    <p class="max-w-2xl text-center text-base text-white md:text-lg">Rediscover your Twitter history, one random tweet at a time.</p>
    <Card class="my-8 max-w-2xl bg-white/10 p-6 text-white backdrop-blur-sm">
      <div class="flex items-center justify-between">
        <h2 class="text-2xl font-bold">Instructions</h2>
        <button on:click={() => (instructionsExpanded = !instructionsExpanded)} class="p-2">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
            class={`transform transition-transform duration-300 ${instructionsExpanded ? '' : 'rotate-180'}`}>
            <path d="m18 15-6-6-6 6" />
          </svg>
        </button>
      </div>
      {#if instructionsExpanded}
        <div transition:slide={{ duration: 300 }}>
          <ol class="list-inside list-decimal space-y-2 py-4 text-left">
            <li>
              Request your X/Twitter data: Navigate to "More" &gt; "Settings and privacy" &gt; "Your
              account" &gt; "Download an archive of your data".
              <p class="mt-1 text-sm text-gray-300">
                NOTE: It can take a while to generate the zip folder. You'll get a notification
                when it's ready.
              </p>
            </li>
            <li>Unzip the downloaded archive.</li>
            <li>Click the 'Choose file' button below.</li>
            <li>
              Navigate to the unzipped folder &gt; data and select the
              <code class="rounded bg-gray-700 px-1 py-0.5 text-sm">tweets.js</code> file.
            </li>
            <li>Click 'Next Tweet' to see a random tweet from your archive.</li>
          </ol>
          <p class="mt-4 text-sm font-semibold text-gray-200">
            NOTE: Twitter Archive Viewer does not store any of your tweet data on the server.
          </p>
        </div>
      {/if}
    </Card>
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

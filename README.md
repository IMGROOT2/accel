<div align="center">
  <img src="https://drive.google.com/uc?export=download&id=11pMeTxjIPw8SvSPKTzXtWXkaPZ6yXgPz" alt="Accel" width="750">
  <b><h4>Your first step for AI optimization.</h4></b>
  <h4>Hosted at <a href="https://accel.ruhangupta.com">accel.ruhangupta.com*</a></h4>
  <p>*There might be bugs! Accel is a work-in-progress. ⚠️</p>
</div>
<br>

<h2>About Accel</h2>
<p>AI is transforming the world around us. Virtually everyone has taken advantage of AI in their own way—whether it’s someone using ChatGPT to generate an email reply or huge tech companies building intelligent systems to change lives.</p>
<p>However, small businesses, like coffee shops and bakeries, lack the resources and expertise to supercharge their businesses with AI. Accel is their first step for AI optimization, offering personalized consultation for small businesses with clear, actionable steps to utilize AI to its fullest advantage. Using Google Cloud’s Speech-to-Text API, Gemini generative models, and intelligent insights from MongoDB, Accel captures conversational input, parses it into discrete tasks, and delivers tailored AI recommendations with no AI background required.</p>

<h3>Dataset Integration</h3>
<p>To benchmark and enrich our recommendations, Accel uses the <a href="https://huggingface.co/datasets/Anthropic/EconomicIndex">Anthropic Economic Index</a> dataset. This collection contains 3,364 real-world economic tasks (e.g., “[Using AI to] analyze financial trends”) annotated with:</p>
<ul>
  <li><code>feedback_loop</code>: % of iterative refinements</li>
  <li><code>directive</code>: % of one-shot commands</li>
  <li><code>learning</code>: % of learning-oriented queries</li>
  <li><code>validation</code>: % of verification checks</li>
</ul>
<p>These metrics guide Accel’s AI prompts, ensuring workflows align with proven patterns of AI-assisted economic tasks. This dataset of economic tasks is crucial for strong, actionable responses. Small businesses often lack the resources to develop a complex AI system, so they frequently rely on prebuilt systems from companies and tailor them to their specific needs.</p>

<h2>Tech Stack</h2>
<ul>
  <li><a href="https://cloud.google.com/speech-to-text">Google Cloud Speech-to-Text</a></li>
  <li><a href="https://cloud.google.com/run">Google Cloud Run</a></li>
  <li><a href="https://www.mongodb.com">MongoDB Atlas</a> for storing peer insights and dataset</li>
  <li><a href="https://gemini.google/overview">Google Gemini</a> for generative parsing & recommendations</li>
  <li><a href="https://nodejs.org">Node.js & Express</a> backend</li>
  <li><a href="https://socket.io">Socket.io</a> for real-time STT streaming</li>
  <li><a href="https://vuejs.org">Vue.js</a> + <a href="https://tailwindcss.com">TailwindCSS</a> frontend</li>
  <li>Axios HTTP client</li>
</ul>

<h1>What I Learned</h1>
<p>Building Accel sharpened my skills in real-time web streaming, containerized deployments on Cloud Run, and secure service-to-service authentication with Workload Identity. I also refined my skills in prompt engineering for practical business applications and deepened my expertise in full-stack Vue.js and Node.js development. I learned about the power of MongoDB’s Atlas Search and how it can be applied to make impactful and smarter tools that benefit a wide range of users.</p>

<h1>The Future of Accel</h1>
<ul>
  <li><b>Multi-user dashboards.</b> Role-based access for business owners, managers, and consultants.</li>
  <li><b>Advanced analytics.</b> Interactive charts to track AI impact on KPIs over time.</li>
  <li><b>Plugin ecosystem.</b> Third-party integrations (Slack, Zapier, CRMs) for seamless automation.</li>
  <li><b>Expanded AI services.</b> Image-based insights, voice-bot agents, and custom model fine-tuning.</li>
  <li><b>Localization.</b> Support for non-English languages to serve global small businesses.</li>
</ul>

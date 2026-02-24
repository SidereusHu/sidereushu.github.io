+++
title = "About Sidereus"
date = 2019-08-26
draft = false
+++


<style>
.card {
  max-width: 960px;
  margin: 2rem auto;
  padding: 2rem;
  display: flex;
  align-items: flex-start;
  gap: 2.5rem;
  background: #f9f9f9;
  border-radius: 16px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.08);
  font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", "PingFang SC", "Microsoft YaHei", sans-serif;
}
.card img {
  width: 320px;
  height: auto;
  object-fit: cover;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin: 0;
  display: block;
}
.card-content {
  flex: 1;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  justify-content: space-between; /* 邮箱与图片底部对齐关键 */
  min-height: 100%; /* 保证内容高度撑满父容器 */
}
.card-content-inner {
  display: flex;
  flex-direction: column;
}
.card-content h1 {
  font-size: 2rem;
  margin-top: 0;
  margin-bottom: 0.4rem;
  word-break: keep-all;
  text-align: left;
  white-space: pre;         /* 空格生效，确保左对齐 */
  font-weight: bold;
  padding-left: 0;
}
.card-content p {
  margin: 0.35rem 0;
  font-size: 1.05rem;
  color: #333;
  line-height: 1.75;
}
.card-content p.intro {
  margin-bottom: 0.12rem;
}
.card-content .founder {
  margin-bottom: 1.8rem;   /* 增加与Honors间距，邮箱能到底部 */
}
.card-content a {
  color: #1a73e8;
  text-decoration: none;
}
.honor-list {
  list-style: disc;
  padding-left: 1.25em;
  margin: 0.2em 0 1em 0;
}
.honor-list li {
  margin: 0.12em 0;
  padding-left: 0;
  text-indent: 0;
  line-height: 1.3;
}
.email-list {
  display: flex;
  flex-direction: column;
  gap: 0.13em;
  margin-top: 0.18em;
}
.email-list a {
  font-size: 1.03rem;
  text-decoration: none;    /* 去掉下划线 */
}
</style>

<div class="card">
  <img src="/images/me.jpg" alt="我的头像">
  <div class="card-content">
    <div class="card-content-inner">
      <h1>Dr. Sidereus M. Hu        </h1>
      <p class="intro">A crypto PhD building the future of trust. Research interests include: Post-Quantum Cryptography, Privacy-Preserving Computation, and Provable Security.</p>
      <p class="founder">Former Founder, Crypto & InfoSec Team (H7US3C)</p>
      <strong style="margin-bottom:0.1em;">Honors:</strong>
      <ul class="honor-list">
        <li>SJTU Top 10 Outstanding Students of the Year</li>
        <li>Academic Star Award</li>
        <li>Outstanding Dissertation Award</li>
      </ul>
    </div>
    <div class="email-list">
      <a href="mailto:crypto.sidereus@gmail.com">📧 crypto.sidereus@gmail.com</a>
      <a href="mailto:mxhu@teamtps.org">📧 mxhu@teamtps.org</a>
    </div>
  </div>
</div>
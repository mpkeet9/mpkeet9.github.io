---
layout: sidebar
title: Private Link Interactive Decision Guide
description: Interactive walkthrough to help determine if your organization should use Azure Private Link with Snowflake.
---

# Azure Private Link Interactive Decision Guide

Welcome! This interactive guide will help you decide if Azure Private Link is right for your Snowflake deployment.

<div id="decision-guide-app"></div>

<script>
  // Simple interactive decision tree (vanilla JS, no frameworks)
  const questions = [
    {
      text: "Do you have strict compliance or regulatory requirements (e.g., HIPAA, PCI, financial data)?",
      yes: "privateLinkRecommended",
      no: 1
    },
    {
      text: "Does your organization require all Snowflake traffic to stay on the Microsoft Backbone (private Azure fiber)?",
      yes: "privateLinkRecommended",
      no: 2
    },
    {
      text: "Are you using managed services like Azure Data Factory or Synapse that need to connect to Snowflake?",
      yes: "adfGuide",
      no: 3
    },
    {
      text: "Is your security team requesting private endpoints for all critical cloud resources?",
      yes: "privateLinkRecommended",
      no: "publicOk"
    }
  ];

  const results = {
    privateLinkRecommended: {
      text: "Azure Private Link is recommended for your use case.",
      animation: "✅"
    },
    adfGuide: {
      text: "Use the <a href='/cloud-providers/azure/private-link/azure-private-link-adf-synapse.html'>ADF & Synapse Private Link Guide</a>.",
      animation: "🔗"
    },
    publicOk: {
      text: "You may use standard public endpoints, but review your organization's security policies.",
      animation: "ℹ️"
    }
  };

  function renderQuestion(idx) {
    const q = questions[idx];
    document.getElementById('decision-guide-app').innerHTML = `
      <div style="animation: fadeIn 0.5s;">
        <h2 style="font-size:1.2em;">${q.text}</h2>
        <button onclick="window.decisionGuideNext('${q.yes}')" style="margin:1em 1em 1em 0;">Yes</button>
        <button onclick="window.decisionGuideNext('${q.no}')">No</button>
      </div>
    `;
  }

  function renderResult(key) {
    const r = results[key];
    document.getElementById('decision-guide-app').innerHTML = `
      <div style="animation: fadeIn 0.5s;">
        <div style="font-size:2.5em;">${r.animation}</div>
        <p style="font-size:1.1em;margin-top:1em;">${r.text}</p>
        <button onclick="window.decisionGuideRestart()" style="margin-top:2em;">Restart</button>
      </div>
    `;
  }

  window.decisionGuideNext = function(next) {
    if (typeof next === "string" && results[next]) {
      renderResult(next);
    } else {
      renderQuestion(next);
    }
  };

  window.decisionGuideRestart = function() {
    renderQuestion(0);
  };

  // Initial render
  renderQuestion(0);
</script>

<style>
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px);}
  to { opacity: 1; transform: translateY(0);}
}
#decision-guide-app button {
  font-size: 1em;
  padding: 0.5em 1.2em;
  border-radius: 5px;
  border: 1px solid #1abc9c;
  background: #f5f6fa;
  color: #155799;
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
}
#decision-guide-app button:hover {
  background: #1abc9c;
  color: #fff;
}
</style>

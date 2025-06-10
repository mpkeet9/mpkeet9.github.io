---
layout: sidebar
title: Private Link Interactive Decision Guide
description: Interactive walkthrough to help determine if your organization should use Private Link with Snowflake.
---

# Private Link Interactive Decision Guide

<div class="center-content">
  <p style="margin-bottom: 1.5em;"></p>
  Welcome! This interactive guide will help you decide if Private Link is right for your Snowflake deployment.
  <p style="margin-bottom: 2.5em;"></p>
  <div id="decision-guide-app"></div>
</div>

<script>
  // Step 1: Ask for cloud provider
  const clouds = [
    { name: "AWS", key: "aws" },
    { name: "Azure", key: "azure" },
    { name: "Google Cloud", key: "gcp" }
  ];

  // Step 2: Decision trees for each cloud
  const questions = {
    aws: [
      {
        text: "Do you have strict compliance or regulatory requirements (e.g., HIPAA, PCI, financial data)?",
        yes: "privateLinkRecommendedAWS",
        no: 1
      },
      {
        text: "Does your organization require all Snowflake traffic to stay on the AWS Backbone (private AWS fiber)?",
        yes: "privateLinkRecommendedAWS",
        no: 2
      },
      {
        text: "Are you using external functions in AWS that need to connect to Snowflake?",
        yes: "privateLinkWithPrivateEndpoint",
        no: 3
      },
      {
        text: "Is your security team requesting private endpoints for all critical cloud resources?",
        yes: "privateLinkRecommendedAWS",
        no: "publicOk"
      }
    ],
    azure: [
      {
        text: "Do you have strict compliance or regulatory requirements (e.g., HIPAA, PCI, financial data)?",
        yes: "privateLinkRecommendedAzure",
        no: 1
      },
      {
        text: "Does your organization require all Snowflake traffic to stay on the Microsoft Backbone (private Azure fiber)?",
        yes: "privateLinkRecommendedAzure",
        no: 2
      },
      {
        text: "Are you using managed services in Azure like Azure Data Factory or Synapse that need to connect to Snowflake?",
        yes: "adfGuide",
        no: 3
      },
      {
        text: "Is your security team requesting private endpoints for all critical cloud resources?",
        yes: "privateLinkRecommendedAzure",
        no: "publicOk"
      }
    ],
    gcp: [
      {
        text: "Do you have strict compliance or regulatory requirements (e.g., HIPAA, PCI, financial data)?",
        yes: "privateLinkRecommendedGCP",
        no: 1
      },
      {
        text: "Does your organization require all Snowflake traffic to stay on the Google backbone (private GCP fiber)?",
        yes: "privateLinkRecommendedGCP",
        no: 2
      },
      {
        text: "Is your security team requesting private endpoints for all critical cloud resources?",
        yes: "privateLinkRecommendedGCP",
        no: "publicOk"
      }
    ]
  };

  const results = {
    privateLinkRecommendedAWS: {
      text: "AWS PrivateLink is recommended for your use case.<br><a href='/cloud-providers/aws/private-link/aws-private-link.html'>See the AWS PrivateLink setup guide</a>.",
      animation: `<svg viewBox="0 0 52 52" width="48" height="48"><circle cx="26" cy="26" r="25" fill="none" stroke="#1abc9c" stroke-width="3"/><path fill="none" stroke="#1abc9c" stroke-width="4" d="M14 27 l7 7 l17 -17"><animate attributeName="d" dur="0.6s" from="M14 27 l0 0 l0 0" to="M14 27 l7 7 l17 -17" fill="freeze"/></path></svg>`
    },
    privateLinkWithPrivateEndpoint: {
      text: "You should consider the <a href='/cloud-providers/aws/private-link/aws-private-link.html'>AWS PrivateLink Guide</a> or the <a href='/cloud-providers/aws/private-link/aws-private-link-with-private-endpoints.html'>AWS PrivateLink Guide with private endpoints</a>.",
      animation: `<svg viewBox="0 0 52 52" width="48" height="48"><circle cx="26" cy="26" r="25" fill="none" stroke="#1abc9c" stroke-width="3"/><path fill="none" stroke="#1abc9c" stroke-width="4" d="M14 27 l7 7 l17 -17"><animate attributeName="d" dur="0.6s" from="M14 27 l0 0 l0 0" to="M14 27 l7 7 l17 -17" fill="freeze"/></path></svg>`
    },
    privateLinkRecommendedAzure: {
      text: "Azure Private Link is recommended for your use case.<br><a href='/cloud-providers/azure/private-link/azure-private-link-generic.html'>See the Azure Private Link setup guide</a>.",
      animation: `<svg viewBox="0 0 52 52" width="48" height="48"><circle cx="26" cy="26" r="25" fill="none" stroke="#1abc9c" stroke-width="3"/><path fill="none" stroke="#1abc9c" stroke-width="4" d="M14 27 l7 7 l17 -17"><animate attributeName="d" dur="0.6s" from="M14 27 l0 0 l0 0" to="M14 27 l7 7 l17 -17" fill="freeze"/></path></svg>`
    },
    adfGuide: {
      text: "Use the <a href='/cloud-providers/azure/private-link/azure-private-link-adf-synapse.html'>ADF & Synapse Private Link Guide</a>.",
      animation: `<svg viewBox="0 0 52 52" width="48" height="48"><circle cx="26" cy="26" r="25" fill="none" stroke="#1abc9c" stroke-width="3"/><path fill="none" stroke="#1abc9c" stroke-width="4" d="M14 27 l7 7 l17 -17"><animate attributeName="d" dur="0.6s" from="M14 27 l0 0 l0 0" to="M14 27 l7 7 l17 -17" fill="freeze"/></path></svg>`
    },
    privateLinkRecommendedGCP: {
      text: "GCP Private Service Connect is recommended for your use case.<br><a href='/cloud-providers/gcp/private-service-connect.html'>See the GCP Private Service Connect setup guide</a>.",
      animation: `<svg viewBox="0 0 52 52" width="48" height="48"><circle cx="26" cy="26" r="25" fill="none" stroke="#1abc9c" stroke-width="3"/><path fill="none" stroke="#1abc9c" stroke-width="4" d="M14 27 l7 7 l17 -17"><animate attributeName="d" dur="0.6s" from="M14 27 l0 0 l0 0" to="M14 27 l7 7 l17 -17" fill="freeze"/></path></svg>`
    },
    publicOk: {
      text: "You may use standard public endpoints, but review your organization's security policies.",
      animation: `<svg viewBox="0 0 52 52" width="48" height="48"><circle cx="26" cy="26" r="25" fill="none" stroke="#e1e4e8" stroke-width="3"/><text x="26" y="34" text-anchor="middle" font-size="28" fill="#155799">i</text></svg>`
    }
  };

  let selectedCloud = null;

  function renderCloudSelector() {
    document.getElementById('decision-guide-app').innerHTML = `
      <div style="animation: fadeIn 0.5s;">
        <h2 style="font-size:1.2em;">Which cloud provider are you using?</h2>
        ${clouds.map(c => `<button onclick="window.decisionGuideSelectCloud('${c.key}')" style="margin:1em 1em 1em 0;">${c.name}</button>`).join('')}
      </div>
    `;
  }

  function renderQuestion(idx) {
    const q = questions[selectedCloud][idx];
    document.getElementById('decision-guide-app').innerHTML = `
      <div style="animation: fadeIn 0.5s;">
        <h2 style="font-size:1.2em;">${q.text}</h2>
        <button onclick="window.decisionGuideNext('${q.yes}')" style="margin:1em 1em 1em 0;">Yes</button>
        <button onclick="window.decisionGuideNext('${q.no}')">No</button>
        <div style="margin-top:4.5em;">
          <button onclick="window.decisionGuideRestart()">Restart</button>
        </div>
      </div>
    `;
  }

  function renderResult(key) {
    const r = results[key];
    document.getElementById('decision-guide-app').innerHTML = `
      <div style="animation: fadeIn 0.5s;">
        <div style="display:flex;align-items:center;justify-content:flex-start;">
          <span style="margin-right:1em;">${r.animation}</span>
          <p style="font-size:1.1em;margin-top:1em;margin-bottom:1em;">${r.text}</p>
        </div>
        <button onclick="window.decisionGuideRestart()" style="margin-top:2em;">Restart</button>
      </div>
    `;
  }

  window.decisionGuideSelectCloud = function(cloudKey) {
    selectedCloud = cloudKey;
    renderQuestion(0);
  };

  window.decisionGuideNext = function(next) {
    if (typeof next === "string" && results[next]) {
      renderResult(next);
    } else {
      renderQuestion(next);
    }
  };

  window.decisionGuideRestart = function() {
    selectedCloud = null;
    renderCloudSelector();
  };

  // Initial render
  renderCloudSelector();
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
/* Center content under the title */
.center-content {
  text-align: center;
}
</style>

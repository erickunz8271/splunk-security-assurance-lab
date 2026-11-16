# Splunk Security Assurance Lab

Can a customer verify that failed SSH sign-ins were logged? In the Buttercup Games sample, yes. Two inspected events record password failures on `mailsv1`. The same evidence leaves two related questions open: which other servers send logs and how long those logs are kept.

## Evidence

Both events displayed **November 15, 2026 at 8:36:59 AM** in Splunk. The screenshot does not show the time zone. These are simulated events from [Splunk’s Buttercup Games tutorial](https://help.splunk.com/en/splunk-enterprise/get-started/search-tutorial/10.6/part-1-getting-started/what-you-need-for-this-tutorial).

| Server in raw event | SSH process | Source IP and port | Recorded outcome |
|---|---|---|---|
| `mailsv1` | `sshd[5276]` | `194.8.74.23:3351` | Failed password for invalid user `appserver` |
| `mailsv1` | `sshd[1039]` | `194.8.74.23:3768` | Failed password for `root` |

Splunk displayed `host=127.0.0.1` for both rows. The original SSH messages identify `mailsv1` as the reporting server. A report grouped only by the indexed `host` field would label both events `127.0.0.1`.

## Reproduce the search

With the tutorial data loaded, set the time picker to include the sample date. Run this SPL and compare `host` with the server name in `_raw`.

```spl
source="*secure.log*" "Failed password" "194.8.74.23" "mailsv1"
| table _time host source _raw
```

## AI-assisted questionnaire review

ChatGPT drafted three mock customer answers and checked them against the two visible events. Independent human review of these answers is pending.

| Customer question | AI draft | Evidence check |
|---|---|---|
| Are failed SSH sign-ins logged? | The sample shows two failed sign-ins on `mailsv1`. | Use **at least two**. Both inspected rows document a failure, and the results list continues beyond those rows. |
| Does every server send these logs? | Coverage beyond `mailsv1` is unknown. | Supported. To answer for the fleet, compare the server inventory against recent ingestion records using verified host identities. |
| How long are logs retained? | The retention period is unknown. | Supported. Check the applicable index settings and verify that records from the claimed period are retrievable. |

**Answer for the mock customer:** The sample contains at least two failed SSH password events on `mailsv1`, one for `appserver` and one for `root`. Fleet coverage and retention remain unverified from this sample.

# My-game-
Not needed 
// Assets/Scripts/RaceManager.cs
using UnityEngine;
using UnityEngine.UI;

public class RaceManager : MonoBehaviour
{
    public static RaceManager Instance;

    [Header("Race")]
    public int lapsToComplete = 3;
    [HideInInspector] public int totalCheckpoints = 0;

    [Header("UI")]
    public Text lapText;
    public Text timerText;

    int nextCheckpoint = 0;
    int completedLaps = 0;
    float lapStartTime = 0f;
    bool raceFinished = false;

    void Awake()
    {
        if (Instance == null) Instance = this;
        else Destroy(gameObject);
    }

    void Start()
    {
        var cps = FindObjectsOfType<Checkpoint>();
        totalCheckpoints = cps.Length;
        lapStartTime = Time.time;
        UpdateLapUI();
    }

    void Update()
    {
        if (raceFinished) return;
        if(timerText != null)
        {
            float t = Time.time - lapStartTime;
            timerText.text = FormatTime(t);
        }
    }

    public void PlayerHitCheckpoint(int idx)
    {
        if (raceFinished) return;
        if (idx != nextCheckpoint) return; // require order

        nextCheckpoint++;
        if (nextCheckpoint >= totalCheckpoints)
        {
            // lap completed
            completedLaps++;
            nextCheckpoint = 0;
            lapStartTime = Time.time; // reset lap timer
            UpdateLapUI();

            if (completedLaps >= lapsToComplete)
            {
                FinishRace();
            }
        }
        else
        {
            UpdateLapUI();
        }
    }

    void UpdateLapUI()
    {
        if (lapText != null) lapText.text = $"Lap: {completedLaps}/{lapsToComplete} (cp {nextCheckpoint}/{totalCheckpoints})";
    }

    void FinishRace()
    {
        raceFinished = true;
        if (lapText != null) lapText.text = $"Finished! Laps: {completedLaps}/{lapsToComplete}";
        if (timerText != null) timerText.text = "00:00.000";
        Debug.Log("Race finished!");
    }

    string FormatTime(float t)
    {
        int minutes = (int)(t / 60f);
        int seconds = (int)(t % 60f);
        int ms = (int)((t * 1000f) % 1000f);
        return $"{minutes:00}:{seconds:00}.{ms:000}";
    }
}

# Claude_lahman

In this exercise we will see how was can use Claude desktop as a source to help create SQL queries.

1. Start by passing the Claude desktop app a copy of the Lahman ERD.  Ask for an interpretation of the ERD if it does not give one

Lahman ERD — Full Interpretation
Key Icons
Gold key = Primary key
Blue cylinder = Regular column
Diamond = Nullable/optional field
Central Table
people — the master player registry

playerid (PK), birth info (year, month, day, country, state, city), death info, name fields (first, last, given), weight, height, bats, throws, debut, finalgame, retroid, bbrefid
Every player-related table links here via playerid

   
2. Write a prompt to ask Claude which tables would be necessary to learn the first and last name of every manager who ever won the TSN Manager of the Year award for both the NL and AL including the teams they were representing for that year.

Claude used 4 tables: 1. people for namefirst and namelast, 2.awardmangers to filter by awardid = 'TSN Manager of the Year' and links via playerid; also has lgid (AL/NL) and yearid. 3. teams for team name linked via teamid and yearid. 4. managers to linked via teamid for that specific year_id

3. Claude should tell you will need the awardsmanager, manager, and people tables at least.   Write a simple query for these 3 tables one at a time and download the realated csv.  Rename the csv to match the table they represent.   Set these csv aside for now.



4. Write a prompt to instruct Claude to write a PostGreSQL query to answer the above question.  Cut and paste that query into the query tool in PGAdmin. Look at the results.  You should have 6 lines of output.  If you have more or less, the query was not working properly.  Examine, the output and query, see where there was a misunderstanding. Try to expand on your prompt or write a new one  to get a better answer. Keep doing this until you get a proper output or you manually fix the query.

SELECT 

    p.namefirst,
    
    p.namelast,
    
    am.yearid,
    
    am.lgid,
    
    t.name AS team_name
    
FROM awardsmanagers am

JOIN people p ON am.playerid = p.playerid

JOIN managers m ON am.playerid = m.playerid AND am.yearid = m.yearid

JOIN teams t ON m.teamid = t.teamid AND m.yearid = t.yearid

WHERE am.awardid = 'TSN Manager of the Year'

AND am.lgid IN ('AL', 'NL')

AND am.playerid IN (

    SELECT playerid
    
    FROM awardsmanagers
    
    WHERE awardid = 'TSN Manager of the Year'
    
    AND lgid IN ('AL', 'NL')
    
    GROUP BY playerid
    
    HAVING COUNT(DISTINCT lgid) = 2
    
)

ORDER BY p.namelast, am.yearid, am.lgid;


results

Davey Jonhson, 1997, Baltimore Orioles

Davey Jonhson, 2012, Washington Nationals

Jim Leyland, 1988, Pittsburgh Pirates

Jim Leyland, 1990, Pittsburgh Pirates

Jim Leyland, 1992, Pittsburgh Pirates

Jim Leyland, 2006, Detroit Tigers

7. Start a new chat (upper left) on Claude desktop.  This time we will pass it 3 text descriptions of the tables.

8. In PGAdmin right-click the Lahman database and select the PSQL tool.  In the interface type the command '\d people', this should output a text description of the people table.  Just copy this output and paste it into the Claude desktop.  Do the same for the managers and awardsmangers tables.

9. Once again ask Claude to write a SQL query to answer question 2.  Was there an difference in the two queries.

10. Now open the people table in Excel.   You can add files to the existing workbook through Claude for Excel.  Add the manager csv as a new sheet then do the same for awardsmanager.  Prompt Claude to create a new sheet that has a list of the managers the meet the criteria mentioned above.  Again, there should be 6 lines of output in the workbook.  If it did not work as expected have Claude update the output to match the actual answer.

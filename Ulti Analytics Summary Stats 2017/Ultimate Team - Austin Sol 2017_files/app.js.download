var ieVersion = getInternetExplorerVersion();
if (ieVersion > 5 && ieVersion < 9) {
	alert("Ewww. We see you are using a version of Internet Explorer prior to version 9 (or running your new version in compatibility mode).  This application hasn't been tested on this browser.  We recommend Chrome, Firefox, Safari or Internet Explorer 9 or above.  You can also use this site on most mobile web browsers.")
}

$('.pagediv').live('pageinit', function(event, data) {
	// TURNED OFF swiping...too sensitive
//	registerPageSwipeHandler('mainpage', 'swipeleft', '#gamespage');
//	registerPageSwipeHandler('gamespage', 'swiperight', '#mainpage');
//	registerPageSwipeHandler('gamespage', 'swipeleft', '#teamstatspage');
//	registerPageSwipeHandler('teamstatspage', 'swiperight', '#gamespage');
});

function registerPageSwipeHandler(pageSource, swipeEvent, pageTarget) {
	$('#' + pageSource).off(swipeEvent).on(swipeEvent, function(event, data) { // off called because need to ensure only one swipe handler
		$.mobile.changePage(pageTarget, {
			transition : 'slide',
			reverse : swipeEvent == 'swiperight'
		});
	});
}

$(document).live('pagebeforeshow', function(event, prevPage) {
	$('.hideWhenBusy').addClass('hidden');  // shown when rest calls finish
});

$(document).live('pagechange', function(event, data) {
	switch (getCurrentPageId()) {
		case 'gamespage':
			renderGamesPage(data);
			break;
		case 'eventspage' :
			renderGameEventsPage(data);
			break;
		case 'gamestatspage' :
			renderGameStatsPage(data);
			break;
		case 'playerstatspage' :
			renderPlayerStatsPage(data);
			break;		
		case 'teamstatspage' :
			renderTeamStats(data);
			break;			
		case 'teamPasswordDialog' :   
			break;	
		case 'unauthorizedpage' :   
			break;				
		default:
			renderMainPage(data);
	}
});

function renderMainPage(data) {
	populateTeam(function() {
		showDeviceBasedTeamPlayerStats();
		isNarrowDevice() ? renderTeamByPlayerStats() : renderTeamPlayerStats();
		enableStatsDownloadLink();
	});
}

function enableStatsDownloadLink() {
	$('.downlaodRawStatsLink').on('click', function(event, data) { 
//		location.href = urlForStatsExportFileDownload(Ultimate.teamId,Ultimate.games);
		location.href = urlForStatsExportFileDownload(Ultimate.teamId); // get all games
	});
}

function renderGamesPage(data) {
	populateTeam(function() {
		populateGamesList();
	});
}

function renderGameEventsPage(data) {
	populateTeam(function() {
		renderGamePageBasics(data)
		populateEventsList();
	});
}

function renderGameStatsPage(data) {
	resetStatsDenomintorChooser($('.gameStatsDenominatorChooser'));
	populateTeam(function() {
		renderGamePageBasics(data);
		populateGamePlayerStats(data);
	});
}

function renderPlayerStatsPage(data) {
	populateTeam(function() {
		renderGamePageBasics(data);
		populateSelectGamesControl();
		populatePlayerStats(data);
	});
}

function renderTeamPlayerStats() {
	resetStatsDenomintorChooser($('.teamStatsDenominatorChooser'));
	populateSelectGamesControl();
	populateTeamPlayerStats();
}

function renderTeamStats(data) {
	populateTeam(function() {
		populateSelectGamesControl();
		populateTeamStats();
	});
}


function renderGamePageBasics(data) {
	Ultimate.gameId = data.options.pageData.gameId;
	$('.gameStatsChoiceLink').attr('href','#gamestatspage?gameId=' + Ultimate.gameId);
	$('.gameEventsChoiceLink').attr('href', '#eventspage?gameId=' + Ultimate.gameId);
}

function resetStatsDenomintorChooser($container) {
	if (Ultimate.statsDenominatorChooserTemplate == null) {
		Ultimate.statsDenominatorChooserTemplate = Handlebars.compile($("#statsDenominatorChooserTemplate").html());
	}
	var html = Ultimate.statsDenominatorChooserTemplate({});
	$container.html(html).trigger('create');
}

function renderTeamByPlayerStats() {
	if (!Ultimate.team) {
		retrieveTeam(Ultimate.teamId, true, function(team) {
			Ultimate.team = team;
			updateTeamByPlayerStats();
		}, handleError) 
	} else {
		updateTeamByPlayerStats();
	}
}

function populateTeam(successFunction) {
	if (isNullOrEmpty(Ultimate.teamId)) {
		$('.teamName').html("Team Not Found");
	} else {
		if (!Ultimate.teamName) {
			retrieveTeam(Ultimate.teamId, true, function(team) {
				Ultimate.team = team;
				Ultimate.teamName = team.name;				
				Ultimate.teamNameWithSeason = team.teamNameWithSeason;
				$('.teamName').html(Ultimate.teamNameWithSeason);
				if (successFunction) {
					successFunction();
				}
			}, handleError);
		} else {
			$('.teamName').html(Ultimate.teamNameWithSeason);
			if (successFunction) {
				successFunction();
			}
		}
	}
}

function updateTeamByPlayerStats() {
	var players = Ultimate.team.players;
		var html = [];
		for ( var i = 0; i < players.length; i++) {
			var player = players[i];
			html[html.length] = '<li><a href="#playerstatspage?name=';
			html[html.length] = encodeURIComponent(player.name);
			html[html.length] = '">'
			html[html.length] = player.name;
			html[html.length] = '</a></li>';
		}
		$("#players").empty().append(html.join('')).listview("refresh");
}

function populateTeamPlayerStats() {
	var gamesToIncludeSelection = $('#selectGamesForTeamPlayerStats option:selected').val();
	gamesToIncludeSelection = gamesToIncludeSelection == null ? 'AllGames' : gamesToIncludeSelection;
	var games = getGamesForSelection(gamesToIncludeSelection);
	retrieveAllStatsForGames(Ultimate.teamId, games, function(allStats) {
		Ultimate.playerStatsHelper = new PlayerStatsHelper({playerStats: allStats.playerStats});
		$('#selectGamesForTeamPlayerStats').val(gamesToIncludeSelection).selectmenu('refresh');
		Ultimate.statType = 'playerName';
		populatePlayerStatsTable();
		$('#selectGamesForTeamPlayerStats').unbind('change').on('change', function() {
			populateTeamPlayerStats();
		});
		$('.statDenominatorRadioButtons input').unbind('click').on('click', function() {
			populateTeamPlayerStats();
		});
	}, handleError); 
}

function populateTeamStats() {
	var gamesToIncludeSelection = $('#selectGamesForTeamStats option:selected').val();
	gamesToIncludeSelection = gamesToIncludeSelection == null ? 'AllGames' : gamesToIncludeSelection;
	var games = getGamesForSelection(gamesToIncludeSelection);
	retrieveAllStatsForGames(Ultimate.teamId, games, function(allStats) {
		Ultimate.teamStatsHelper = new TeamStatsHelper({teamStats: allStats.teamStats});
		$('#selectGamesForTeamStats').val(gamesToIncludeSelection).selectmenu('refresh');
		populateTeamStatsGraphs();
		$('#selectGamesForTeamStats').unbind('change').on('change', function() {
			populateTeamStats();
		});
	}, handleError); 
}

function populateTeamStatsGraphs() {
	Ultimate.teamStatsHelper.renderGoalsSummaryPieCharts();
	Ultimate.teamStatsHelper.renderTrendGraph();
	Ultimate.teamStatsHelper.renderGoalPerOpportunityGraph();
	Ultimate.teamStatsHelper.renderBreaksGraph();
}

function showDeviceBasedPlayerStats() {
	var isNarrow = isNarrowDevice();
	$('#playerStatsNarrow').toggleClass('hidden', !isNarrow);
	$('#playerStatsWide').toggleClass('hidden', isNarrow);
	$('.statDenominatorRadioButtons').toggleClass('hidden', isNarrow);
	$('#gamespageDataSelection').toggleClass('ui-block-a', !isNarrow);
}

function showDeviceBasedTeamPlayerStats() {
	var isNarrow = isNarrowDevice();
	$('#teamStatsNarrow').toggleClass('hidden', !isNarrow);
	$('#teamStatsWide').toggleClass('hidden', isNarrow);
}

function populateGamesList() {
	if (!Ultimate.games) {
		retrieveGames(Ultimate.teamId, function(games) {
			Ultimate.games = games;
			updateGamesList(Ultimate.games);
		}, handleError) 
	} else {
		updateGamesList(Ultimate.games);
	}
}

function updateGamesList(games) {
	var sortedGames = sortGames(games);
	var html = [];
	for ( var i = 0; i < sortedGames.length; i++) {
		var game = sortedGames[i];
		html[html.length] = '<li><a href="#eventspage?gameId=';
		html[html.length] =  game.gameId;
		html[html.length] = '">';
		html[html.length] = '<span class="game-date">';
		html[html.length] = game.date;
		html[html.length] = '&nbsp;&nbsp;';
		html[html.length] = game.time;
		html[html.length] = '</span>&nbsp;&nbsp;';		
		html[html.length] = '<span class="opponent">vs. ';
		html[html.length] = game.opponentName;
		html[html.length] = '</span>';
		html[html.length] = '<span class="tournament">';
		html[html.length] = isBlank(game.tournamentName) ?  '&nbsp;&nbsp;&nbsp;' : '&nbsp;&nbsp;at ' + game.tournamentName;
		html[html.length] = '</span>&nbsp;&nbsp;';		
		html[html.length] = '<span class="score '; 
		html[html.length] = game.ours > game.theirs ? 'ourlead' : game.theirs > game.ours ? 'theirlead' : ''; 
		html[html.length] = '">';
		html[html.length] = game.ours;
		html[html.length] = '-';
		html[html.length] = game.theirs;
		html[html.length] = '</span>';
		html[html.length] = '</a></li>';
	}
	$("#games").empty().append(html.join('')).listview("refresh");
}

function isBlank(s) {
	return s == null || s == '';
}

function populateEventsList() {
	retrieveGame(Ultimate.teamId, Ultimate.gameId, function(game) {
		Ultimate.game = game;
		updateGamePointsList(Ultimate.game);
		populateGameTitle();
	}, handleError) 
}

function populateGamePlayerStats(data) {
	retrieveGame(Ultimate.teamId, Ultimate.gameId, function(game) {
		Ultimate.game = game;
		populateGameTitle();
		retrievePlayerStatsForGames(Ultimate.teamId, [Ultimate.gameId], function(playerStatsArray) {
			Ultimate.statType = 'playerName';
			Ultimate.playerStatsHelper = new PlayerStatsHelper({playerStats: playerStatsArray});
			showDeviceBasedPlayerStats();
			if (isNarrowDevice()) {
				populateMobileGamePlayerStatsData(data.options.pageData.ranktype);
			} else {
				populatePlayerStatsTable();  
				$('.statDenominatorRadioButtons input').unbind('click').on('click', function() {
					populatePlayerStatsTable();
				});
			}
		}, handleError) 
	}) 
}

function populateMobileGamePlayerStatsData(stattype) {
	updatePlayerRankingsTable(stattype);
	$('#selectPlayerRank').unbind('change').on('change', function() {
		updatePlayerRankingsTable($(this).val());
	});
}

function populatePlayerStatsTable() {
	$statsTable = $('.playerStats');
	var statsTable = Ultimate.playerStatsHelper.playerStatsTable(!isAbsoluteDenominator(), Ultimate.statType);
	$statsTable.html(createTeamStatsTableHtml(statsTable));
	$("a[data-stattype='" + Ultimate.statType + "']").addClass('selectedColumn');
	$statsTable.find('th a').off().on('click', function() {
		Ultimate.statType = $(this).data('stattype');
		populatePlayerStatsTable();
	})
}

function populatePlayerStats(data, gamesToIncludeSelection) {
	$('#statsPlayerNameHeading').html('');
	$('#playerStats').hide();
	Ultimate.playerName = decodeURIComponent(data.options.pageData.name);
	var selection = gamesToIncludeSelection == null ? 'AllGames' : gamesToIncludeSelection;
	var games = getGamesForSelection(selection);
	retrievePlayerStatsForGames(Ultimate.teamId, games, function(playerStatsArray) {
		Ultimate.playerStatsHelper = new PlayerStatsHelper({playerStats: playerStatsArray});
		$('#selectGamesForSinglePlayerStats').val(selection).selectmenu('refresh');
		$('#statsPlayerNameHeading').html(Ultimate.playerName);
		updateSinglePlayerStatsTable(Ultimate.playerName);
		$('#playerStats').show();
		$('#selectGamesForSinglePlayerStats').unbind('change').on('change', function() {
			populatePlayerStats(data, $(this).val());
		});
	}, handleError); 
}

function populateSelectGamesControl() {
	if (!Ultimate.games) {
		retrieveGames(Ultimate.teamId, function(games) {
			Ultimate.games = games;
			updateSelectGamesControl(Ultimate.games);
		}, handleError) 
	} else {
		updateSelectGamesControl(Ultimate.games);
	}
}

function updateSelectGamesControl(games) {
	var html = [];

	addGameSelection(html, 'AllGames', 'All Games');
	
	Ultimate.tournaments = getTournaments(games);
	for ( var i = 0; i < Ultimate.tournaments.length; i++) {
		var tournament = Ultimate.tournaments[i];
		var description = tournament.name + ' ' + tournament.year;
		addGameSelection(html, tournament.id, description);
	}	
	
	var sortedGames = sortGames(games);
	for ( var i = 0; i < sortedGames.length; i++) {
		var game = sortedGames[i];
		var description = game.date + (isBlank(game.tournamentName) ?  ' ' : (' at ' + game.tournamentName)) + ' vs. ' +  game.opponentName + ' ';
		addGameSelection(html, game.gameId, description);
	}
	$(".gameSelect").selectmenu().empty().html(html.join('')).selectmenu('refresh');
}

function getGamesForSelection(selectionName) {
	if (selectionName == 'AllGames') {
		return [];
	} else if (selectionName.indexOf('TOURNAMENT-') == 0) {
		for ( var i = 0; i < Ultimate.tournaments.length; i++) {
			if (Ultimate.tournaments[i].id == selectionName) {
				return Ultimate.tournaments[i].games;
			}
		}
		throw 'Tournament ID ' + selectionName + ' not found';
	} else {
		return [selectionName];
	}
}

function addGameSelection(html, value, display) {
	html[html.length] = '<option value="';
	html[html.length] =  value;
	html[html.length] = '">';
	html[html.length] = display;
	html[html.length] = '</option>';
}

function populateGameTitle() {
	var game = Ultimate.game;
	$('.opponentTitle').html('vs. ' + game.opponentName);
	var $score = $('.gameScore');
	$score.html(game.ours + '-' + game.theirs);
	$score.addClass(game.ours > game.theirs ? 'ourlead' : game.theirs > game.ours ? 'theirlead' : '');
	$('.gameDetails').html(game.date + ' ' + game.time + (isBlank(game.tournamentName) ?  '' : ' at ' + game.tournamentName));
}

function updateGamePointsList(game) {
	Ultimate.points = JSON.parse(game.pointsJson).reverse();
	var html = [];
	for ( var i = 0; i < Ultimate.points.length; i++) {
		var point = Ultimate.points[i];
		var score = point.summary.score;
		var elapsedTime = point.summary.elapsedTime;
		elapsedTime = elapsedTime == null ? '' : ' (' + secondsToMinutes(elapsedTime, 1) + ' minutes)';
		html[html.length] = '<div data-role="collapsible" data-index="';
		html[html.length] = i;
		html[html.length] = '"><h3>';
		html[html.length] = score.ours;
		html[html.length] = '-';
		html[html.length] = score.theirs;
		html[html.length] = '&nbsp;&nbsp;';
		html[html.length] = point.summary.lineType == 'O' ? 'O-line' : 'D-line';
		html[html.length] = '&nbsp;&nbsp;';
		html[html.length] = point.summary.finished ? elapsedTime : ' (unfinished point)';
		html[html.length] = '&nbsp;&nbsp;<span class="lineDescription">';
		html[html.length] = playersInPointDescription(point.line, point.substitutions);
		html[html.length] = '</span>';
		html[html.length] = '</h3><ul data-role="listview" data-inset="true" data-theme="a"></div>';
	}
	$('#points').empty().append(html.join('')).trigger('create');
	$('#points div[data-role="collapsible"]').live(
			'expand', function() {
				var point = Ultimate.points[$(this).data('index')];
				populatePointEvents($(this));
			});
}

function updatePlayerRankingsTable(rankingType) {
	var rankingType = rankingType == null ? 'pointsPlayed' : rankingType;
	$('#selectPlayerRank').val(rankingType).selectmenu('refresh');
	var rankings = Ultimate.playerStatsHelper.playerRankingsFor(rankingType);
	var html = [];
	var statDescription = $("#selectPlayerRank :selected").text();
	addRowToStatsTable(html,'<strong>Player</strong>','<strong>' + statDescription + '</strong>', 
			Ultimate.playerStatsHelper.isPerPointStat(rankingType) ? '<strong>per point played</strong>' : '');
	addRowToStatsTable(html,'&nbsp;','&nbsp;');
	var total = 0;
	for (var i = 0; i < rankings.length; i++) {
		var value = rankings[i].value;
		if (rankingType == 'secondsPlayed') {
			value = secondsToMinutes(value);
		}
		var perPoint = rankings[i].perPoint;
		addRowToStatsTable(html,rankings[i].playerName, value, perPoint);
		total += value;
	}
	if (rankingType.indexOf("Played") < 0)	 {
		addRowToStatsTable(html,'&nbsp;','&nbsp;');
		addRowToStatsTable(html,'<strong>Total</strong>', '<strong>' + total + '</strong>', '&nbsp;');
	}
	$('#playerRankings tbody').html(html.join(''));
}

function addRowToStatsTable(html, name, stat1, stat2) {
	html[html.length] = '<tr><td class="statsTableDescriptionColumn">';
	html[html.length] = name;
	html[html.length] = '</td><td class="statsTableValueColumn1">';
	html[html.length] = stat1;
	html[html.length] = '</td><td class="statsTableValueColumn2">';
	html[html.length] = stat2;	
	html[html.length] = '</td></tr>';
}

function updateSinglePlayerStatsTable(playerName) {
	var absolutePlayerStats = Ultimate.playerStatsHelper.statsForPlayer(playerName, false);
	var html = [];
	if (absolutePlayerStats) {
		var headings = Ultimate.playerStatsHelper.getStatLabelLookup();
		var perPointPlayerStats = Ultimate.playerStatsHelper.statsForPlayer(playerName, true);
		addRowToStatsTable(html,'<strong>Statistic</strong>','<strong>Value</strong>','<strong>Per Point Played</strong>');
		addRowToStatsTable(html,'&nbsp;','&nbsp;');
		addRowToStatsTable(html,headings.plusMinusCount,absolutePlayerStats.plusMinusCount, perPointPlayerStats.plusMinusCount);
		addRowToStatsTable(html,headings.plusMinusOLine,absolutePlayerStats.plusMinusOLine);
		addRowToStatsTable(html,headings.plusMinusDLine,absolutePlayerStats.plusMinusDLine);
		addRowToStatsTable(html,headings.gamesPlayed,absolutePlayerStats.gamesPlayed);
		addRowToStatsTable(html,headings.pointsPlayed,absolutePlayerStats.pointsPlayed);
		addRowToStatsTable(html,headings.opointsPlayed,absolutePlayerStats.opointsPlayed);
		addRowToStatsTable(html,headings.dpointsPlayed,absolutePlayerStats.dpointsPlayed);
		addRowToStatsTable(html,headings.minutesPlayed,absolutePlayerStats.minutesPlayed);
		addRowToStatsTable(html,headings.touches,absolutePlayerStats.touches, perPointPlayerStats.touches);
		addRowToStatsTable(html,headings.goals,absolutePlayerStats.goals, perPointPlayerStats.goals);
		addRowToStatsTable(html,headings.callahans,absolutePlayerStats.callahans, perPointPlayerStats.callahans);
		addRowToStatsTable(html,headings.assists,absolutePlayerStats.assists, perPointPlayerStats.assists);
		addRowToStatsTable(html,headings.passes,absolutePlayerStats.passes, perPointPlayerStats.passes);
		addRowToStatsTable(html,headings.catches,absolutePlayerStats.catches, perPointPlayerStats.catches);
		addRowToStatsTable(html,headings.drops,absolutePlayerStats.drops, perPointPlayerStats.drops);
		addRowToStatsTable(html,headings.throwaways,absolutePlayerStats.throwaways, perPointPlayerStats.throwaways);
		addRowToStatsTable(html,headings.stalls,absolutePlayerStats.stalls, perPointPlayerStats.stalls);
		addRowToStatsTable(html,headings.miscPenalties,absolutePlayerStats.miscPenalties, perPointPlayerStats.miscPenalties);
		addRowToStatsTable(html,headings.callahaneds,absolutePlayerStats.callahaneds, perPointPlayerStats.callahaneds);
		addRowToStatsTable(html,headings.passSuccess,absolutePlayerStats.passSuccess);
		addRowToStatsTable(html,headings.catchSuccess,absolutePlayerStats.catchSuccess);
		addRowToStatsTable(html,headings.ds,absolutePlayerStats.ds, perPointPlayerStats.ds);
		addRowToStatsTable(html,headings.pulls,absolutePlayerStats.pulls, perPointPlayerStats.pulls);
		addRowToStatsTable(html,headings.pullsAvgHangtimeMillis,absolutePlayerStats.pullsAvgHangtimeMillis);
		addRowToStatsTable(html,headings.pullsOB,absolutePlayerStats.pullsOB);
	} else {
		addRowToStatsTable(html,'No Data','');
	}

	$('#playerStats tbody').html(html.join(''));
}

function populatePointEvents($pointEl) {
	var point = Ultimate.points[$pointEl.data('index')];
	var events = point.events.slice().reverse();
	var html = [];
	for ( var i = 0; i < events.length; i++) {
		var event = events[i];
		var description = eventDescription(event);
		html[html.length] = '<li data-theme="';
		html[html.length] = event.type == 'Offense' ? 'a' : 'b';
		html[html.length] = '">';
		html[html.length] = '<img src="/images/' + description.image + '" class="listImage">&nbsp;&nbsp;';
		html[html.length] = description.text;
		html[html.length] = '</li>';
	}
	$pointEl.find('ul[data-role="listview"]').empty().append(html.join(''))
			.listview("refresh");
}

function eventDescription(event) {
	switch (event.action) {
		case 'Catch':
			return {text: event.passer + ' to ' + event.receiver, image: 'big_smile.png'};
		case 'Drop' :
			return {text: event.receiver + ' dropped from ' + event.passer, image: 'eyes_droped.png'};
		case 'Throwaway':
			return {text: event.type == 'Offense' ? event.passer + ' throwaway' : 'Opponent throwaway', 
					image: event.type == 'Offense' ? 'shame.png' : 'exciting.png'};
		case 'Stall':
			return {text: event.passer + ' stalled', image: 'shame.png'};
		case 'MiscPenalty':
			return {text: event.passer + ' penalized (caused turnover)', image: 'shame.png'};			
		case 'D' :
			return {text: 'D by ' + event.defender, image: 'electric_shock.png'};
		case 'Pull' :
			return {text: 'Pull by ' + event.defender, image: 'nothing.png'};		
		case 'PullOb' :
			return {text: 'Pull (Out of Bounds) by ' + event.defender, image: 'what.png'};				
		case 'Goal':
			return {text: event.type == 'Offense' ? 
					'Our Goal (' + event.passer + ' to ' + event.receiver + ')' :
					'Their Goal', image: event.type == 'Offense' ? 'super_man.png' : 'cry.png'};		
		case 'Callahan':
			return {text: 'Our Callahan (' + event.defender + ')', image: 'victory.png'};		
		case 'EndOfFirstQuarter':
			return {text: 'End of 1st Quarter', image: 'stopwatch1.png'};		
		case 'EndOfThirdQuarter':
			return {text: 'End of 3rd Quarter', image: 'stopwatch1.png'};		
		case 'EndOfFourthQuarter':
			return {text: 'End of 4th Quarter', image: 'stopwatch1.png'};	
		case 'EndOfOvertime':
			return {text: 'End of an Overtime', image: 'stopwatch1.png'};				
		case 'Halftime':
			return {text: 'Halftime', image: 'stopwatch1.png'};		
		case 'GameOver':
			return {text: 'Game Over', image: 'finishflag.png'};		
		case 'Timeout':
			return {text: 'Timeout', image: 'stopwatch1.png'};		
			
		default:
			return {text: event.action, image: 'hearts.png'};
	}
}

function secondsToMinutes(seconds, decimalPositions) {
	return decimalPositions ? (seconds/60).toFixed(decimalPositions) : Math.round(seconds / 60);
	return decimalPositions ? seconds/60 : Math.round(seconds / 60);
}

function createTeamStatsTableHtml(statsTable) {
	if (Ultimate.teamStatsTemplate == null) {
		Ultimate.teamStatsTemplate = Handlebars.compile($("#playerStatsTableTemplate").html());
	    Handlebars.registerHelper('stripeRows', function (rows, fn) {
	        var buffer = [],
	            i, len;
	        
	        for (i = 0, len = rows.length; i < len; ++i) {
	            var row = rows[i];
	            row.even = (i + 1) % 2 === 0 ? true : false;
	            
	            // Render the block once for each row.
	            buffer.push(fn(row));
	        }
	        
	        return buffer.join('');
	    });
	}
	return Ultimate.teamStatsTemplate(statsTable);
}

function isAbsoluteDenominator() {
	return $('#' + getCurrentPageId() + ' .statDenominatorRadioButtons input:checked').attr('value') == 'Absolute';	
}

function isNarrowDevice() {
	return screen.width < 500; // equivalent to media query device-width 
	//return document.documentElement.clientWidth < 500;  // equivalent to media query width 
//	return true;
}

function getCurrentPageId() {
	return $.mobile.activePage.attr('id');
}

function handleError(jqXHR, textStatus, errorThrown) {
	if (jqXHR.status == 401) {
		requestSignon();
	} else {
		alert("Whoops! Sorry, we seemed to be having some problems on our server.  Please try again soon but let us know if this happens again.");
	}

}

function requestSignon() {
	$.mobile.changePage('#teamPasswordDialog', {transition: 'pop', role: 'dialog'});
	$('#teamName').html(Ultimate.teamName);
	$('#passwordErrorMessage').html("");
	$('#passwordSubmitButton').unbind().on('click', function() {
		var password = $('#teamPasswordInput').val();
		signon(Ultimate.teamId, password, function() {
			$.mobile.changePage('#mainpage', {transition: 'pop'});
		}, function() {
			$('#passwordErrorMessage').html("Incorrect Password");
		});
	});
	$('#passwordCancelButton').unbind().on('click', function() {
		$.mobile.changePage('#unauthorizedpage', {transition: 'pop'});
	});
}

function playersInPointDescription(line, substitutions) {
	var players = playersInPoint(line, substitutions);
	var desc = '';
	for ( var playerName in players) {
		if (desc != '') {
			desc += ', ';
		}
		desc += playerName;
		if (!players[playerName]) {
			desc += ' (partial)';
		}
	}
	return desc;
}

function playersInPoint(line, substitutions) {
	// answer an object where property name is player name and property value is true if 
	// played all of point and false if played part of point
	var players = {};
	if (line) {
		// start with players one the line at the end of the point
		for ( var i = 0; i < line.length; i++) {
			players[line[i]] = true;
		}
		// adjust for subs
		if (substitutions) {
			for ( var i = 0; i < substitutions.length; i++) {
				players[substitutions[i].fromPlayer] = false;
				players[substitutions[i].toPlayer] = false;
			}
		}
	}
	
	return players;
}


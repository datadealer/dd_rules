# dd_rules #

A dataset defining game rules and wordings for [Data Dealer](https://datadealer.com) online game.

## Copyright

Copyright (c) 2011-2014, Cuteacute Media OG

`dd_rules` is released under 

[Creative Commons Attribution-ShareAlike 3.0, Österreich (CC-BY-SA 3.0)](http://creativecommons.org/licenses/by-sa/3.0/at/)

## Basic schema description ##

### Default Game ###

Controls the contents of the initial game state.

#### ROOT ####

    {
      "Database": {                        // Database root element
        "children": [],                    // unused
        "instance_data": {}                // unused
      },
      "Imperium": {
        "children": [DATABASE_PERP],       // Creates a Database element in Empire view
        "instance_data": {}                // unused
      }
    }

#### DATABASE_PERP ####

    {
      "full_type": "DatabasePerp:database001", // Don't change this!
      "instance_data": {
        "x": 1024,                         // default position on Empire view
        "y": 800                           // default position on Empire view
      }
    }

### Rules collection ###

#### ROOT ####

    {
      "karmalizers": [KARMALIZER /*, KARMALIZER, … */], // all available karmalizers
      "karmalauters": [KARMALIZER /*, KARMALIZER, …*/], // all available karmalauters
      "levels": [LEVEL /*, LEVEL, … */],                // level information
      "missions": [MISSION /*, MISSION, … */],          // all available missions
      "perps": {                                        // all basic game elements
        DATABASE_PERP,
        AGENT_PERP /*, AGENT_PERP, … */,
        CITY_PERP /*, CITY_PERP, … */,
        CLIENT_PERP /*, CLIENT_PERP, … */,
        CONTACT_PERP /*, CONTACT_PERP, … */,
        PROJECT_PERP /*, PROJECT_PERP, … */,
        PUSHER_PERP /*, PUSHER_PERP, … */
      },
      "powerups": {POWERUP /*, … */},                   // all available Project Powerups
      "tokens": {TOKEN /*, … */},                       // all available Database Tokens
      "version": 1                                      // obsolete/unused, do not touch!
    }

#### LEVEL ####

    {
      ap_inc_interval: 1000,      // ap increment interval in milliseconds
      ap_inc_value: 1,            // ap increment value every ap_inc_interval                     
      ap_max: 10000,              // ap upper cap for level
      number: 2,                  // level number (redundant with list position
      xp_max: 1999,               // levelup to number+1 on xp==xp_max+1
      xp_min: 1000                // lower xp boundary for level
    }

#### COMMON_TYPEDATA ####


    {
      "description": "This is a description.", // Element description
      "label": "Sweepstakes",                  // Element label
      "title": "Sweepstakes",                  // Element title (used in popups)
      "subtitle": "Some subtitle",             // Element subtitle (used in popups)
      "perp_sprite": SPRITE,                   // Element sprite, used on game map, optional
      "popup_sprite": SPRITE,                  // Element sprite, used in popups, optional
      "slot_sprite": SPRITE,                   // Element sprite, used in slot representation, optional
      "price": 100,                            // Cash costs to buy
      "required_level": 1,                     // Level required to obtain item
      "story": {                               // optional
        text: "Wohoo, you've bought a mangificent perp!",
        trigger: "buy",
      }
    }

#### SPRITE ####

    "perp_sprite:" {                        // Or "popup_sprite" for popup sprites
      "frameMap": {                         
        "normal": {                         // a frameMap can contain several 'views'
          "height": 100,                    // height of the image
          "width": 100,                     // width of the image
          "x": 400,                         // x-offset on spritesheet
          "y": 0                            // y-offset on spritesheet
        }
      }, 
      "frameSrc": "sprites-perp.png"        // spritesheet filename
    }

#### TOKEN_REF ####

A token reference enriched with metadata. Impact of provided values may differ depending on context.

    {
      "amount": 100,                        // weight factor, 0..100
      "full_type": "TokenPerp:token001",    // game_type:gestalt
      "gestalt": "token001",                // gestalt id of TOKEN
      "is_required": false,                 // optional     
      "position": 1                         // used for sorting
    }

#### KARMALIZER ####

`karmalauters` describe buyable one-shot image improvments; they should have karma_points>0 and price>0.
`karmalizers` describe 'bad image' incidents. they should have negative `karma_points` and price==0.
`karmalizers` and `karmalauters` provide a `popup_sprite` with a single `normal` view and no `perp_sprite`.


    {
      "game_type": "Karmalauter",           // ...or "Karmalizer"
      "type_data": {
        "gestalt": "karma001",              // globally unique gestalt identifier
        "karma_points": 11                  // image modificator on buy/trigger
      } + COMMON_TYPEDATA
    }

#### DATABASE_PERP ####

Represents a "special" game element - the Database element on Empire viewmap.

    "database001": {                        // gestalt for DATABASE_PERP should ALWAYS be 'database001'
      "game_type": "DatabasePerp",
      "type_data": {
        "description": "I iz in ur databaze, collecting ur profilez!",
        "label": "Database",
        "title": "Database" 
      }
    }

#### AGENT_PERP ####

Represents Agent game elements. AGENT_PERP provides a `perp_sprite` and a `popup_sprite`.

    "agent001": {                                  // globally unique gestalt id
      "game_type": "AgentPerp",
      "type_data": {
        "provided_perps": ["contact001" /*, … */], /* list of gestalt ids of contacts available through 
                                                      this agent */
      } + COMMON_TYPEDATA
    }

#### CITY_PERP ####

Represents City game elements. CITY_PERP provides a `perp_sprite` and a `popup_sprite`.

    "city001": {                         // globally unique gestalt id
      "game_type": "CityPerp",           
      "type_data": {
        "profiles_max": 740000000,       // increases global profiles cap by profile_max
        "profileset_size": 1757353,      // size of the bundled bonus profileset
        // gestalt ids of items available in this city
        "provided_perps": ["agent001", "pusher002", "proxy003" /*, … */],
        // tokens included in bundled bonus profileset
        "tokens": [TOKEN_REF /*, … */]
      } + COMMON_TYPEDATA
    }

#### CLIENT_PERP ####

Represents Client game elements. CLIENT_PERP provides a `perp_sprite` and a `popup_sprite`.
Represents Client game elements. CLIENT_PERP provides a `perp_sprite` and a `popup_sprite`.

    "client001": {                                 // globally unique gestalt id
      "game_type": "ClientPerp",
      "type_data": {
        "charge_time": 10000,                      // charge cycle duration, in ms
        "consumed_tokens": [TOKEN_REF /*, … */],   // tokens used in client output calculations
        "income_base": 1000,                       // minimum client cash output
        "income_factor": 100,                      // client cash output growth factor
        "provided_tokens": ["token001" /* , … */], // gestalt ids of tokens used in client output calculations
        // gestalt ids of items which are required for client to be purchased
        "required_providers": ["contact001", "project002", 'supertoken003' /*, …*/],
        "requirements_text": "Descriptive text about which requirements need to be fulfilled to unlock this client." 
      } + COMMON_TYPEDATA
    }

#### CONTACT_PERP ####

Represents Contact game elements. CONTACT_PERP provides a `perp_sprite` and a `popup_sprite`.

    "contact001": {                                 // globally unique gestalt id
      "game_type": "ContactPerp",
      "type_data": {
        "charge_cost": 350,                         // cash costs of a charge cycle
        "charge_time": 6000,                        // charge cycle duration, in ms
        "collect_amount": 4000,                     // base output amount, in profiles
        "collect_risk": 35,                         // image penalty on collect
        "tokens": [TOKEN_REF /*, … */]              // tokens included in output profileset
      } + COMMON_TYPEDATA
    }

#### PROXY_PERP ####

Represents Proxy game elements. PROXY_PERP provides a `perp_sprite` and a `popup_sprite`.

    "proxy001": {                                   // globally unique gestalt id
      "game_type": "ProxyPerp",
      "provided_perps": ["project001" /*, … */],    // gestalt ids of projects provided by proxy
      "type_data": {
        "max_slots": 3                              // maximum projects per proxy  
      } + COMMON_TYPEDATA
    }

#### PUSHER_PERP ####

Represents Pusher game elements. PUSHER_PERP provides a `perp_sprite` and a `popup_sprite`.

    "pusher001": {                                  // globally unique gestalt id
      "game_type": "PusherPerp",
      "type_data": {
        provided_clients: ["client003" /*, … */],   // gestalt ids of clients provided by pusher
      } + COMMON_TYPEDATA
    }

#### PROJECT_PERP ####

Represents Project game elements. PROJECT_PERP provides a `perp_sprite` and a `popup_sprite`.

    "project001": {                                 // globally unique gestalt id
      "game_type": "ProjectPerp",
      "type_data": {
        "charge_cost": 350,                         // basic charge cycle cash costs
        "charge_time": 6000,                        // basic charge cycle duration in ms
        "collect_amount": 4000,                     // basic collect amount in profiles
        "collect_risk": 35,                         // image penalty on collect
        "upgrade_slots": 9,                         // default slots for upgrade powerups
        "ad_slots": 3,                              // default slots for ad powerups
        "teammember_slots": 3,                      // default slots for teammember powerups
        "max_upgrade_slots": 22,                    // maximum slots for upgrade powerups 
        "max_ad_slots": 15,                         // maximum slots for ad powerups
        "max_teammember_slots": 17,                 // maximum slots for teammember powerups
        "provided_ads": [POWERUP_REF /*, … */],     // available ad powerups
        "provided_teammembers": [POWERUP_REF /*, … */], // available teammembers powerups
        "provided_upgrades": [POWERUP_REF /*, … */],// available upgrade powerups
        "rename_ads_tab": false,                    // true if the “Ads” tab should display “Servers” (unused?)
        "slot_cost": 1000,                          // powerup slot cash costs
        "slot_cost_modifier": 1,                    // undocumented
        "tokens": [TOKEN_REF /*, … */],             // tokens in basic collect output profileset
      } + COMMON_TYPEDATA
    }

#### POWERUP ####

Represents Project Powerups. Gestalt ids should be prefixed 'ad' for Ads, 'upgrade' for upgrades and 'teammember' for teammembers.
POWERUP provides a `popup_sprite` and a `slot_sprite`.

    "ad001": {                          // globally unique gestalt id
      "game_type": "AdPowerup",         // ...or "TeamMemberPowerup", "UpgradePowerup"
      "type_data": {
        "tokens": [TOKEN_REF /*, … */]  // Extra tokens provided by powerup
      } + COMMON_TYPEDATA
    }

#### POWERUP_REF ####

Represents a project-powerup relations. Defines, which effect a powerup has on project behaviour.

    {
      "charge_cost_modifier": 40,       // modifies charge_cost of project by...
      "collect_amount_modifier": 11000, // modifies collect_amount of project by...
      "collect_risk_modifier": 0,       // modifies collect_risk of project by...
      "full_type": "AdPowerup:ad001",   // game_type:gestalt
      "gestalt": "ad001",               // gestalt id of powerup
      "notification": false,            // true if notification should be displayed when item is unlocked
      "price": 800,                     // cash amount required to buy powerup for a given project
      "required_level": 10              // level required to buy powerup for a given project
    }

#### TOKEN ####

Represents a 'database token'. TOKEN_PERP provides a `perp_sprite` and a `popup_sprite`.

    "token001": {                                // globally unique gestalt id
      "game_type": "TokenPerp",
      "type_data": {
        "charge_time": 10000,                    // charge cycle duration in ms, optional
        "contained_tokens": [TOKEN_REF /*, … */],// optional, tokens which can be used to improve give tokens' amount
        "findings_text": "You get one or more IP addresses of %s people",
        "is_buyable": false,                     // can be purchased
        "is_supertoken": false,                  // unused?
        "knowledge_text": "You know one or more IP addresses of %s people",
        "origin_full_type": "ContactPerp:contact001", // optional, for special origin-tokens only
        "origin_gestalt": "contact001",               // optional, for special origin-tokens only
        "requirements_text": "Descriptive text about the requirements for unlocking this item." 
      } + COMMON_TYPEDATA
    }

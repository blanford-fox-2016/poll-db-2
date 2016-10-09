#Release 0

1.
insert into voters (first_name, last_name, gender, party, party_duration, age, married, children_cout, homeowner, employed, created_at, updated_at)
values ('ken', 'putra', 'male', 'democrat', 1, 22, 0, 0, 0, 0, 0, 0);

insert into votes (voter_id, politician_id, created_at, updated_at)
values (50001, 24, 0, 0);

insert into voters (first_name, last_name, gender, party, party_duration, age, married, children_cout, homeowner, employed, created_at, updated_at)
values ('dui', 'putra', 'male', 'democrat', 1, 22, 0, 0, 0, 0, 0, 0);

insert into votes (voter_id, politician_id, created_at, updated_at)
values (50002, 274, 0, 0);

2.
update congress_members
set name = 'Donald Trump'
where name = 'Sen. Frank Lautenberg';

# Release 1

1.
select voters.first_name,
voters.last_name,
voters.party,
count(votes.voter_id) as vote_count
from voters
join votes
on voters.id = votes.voter_id
where party != 'democrat'
and party != 'republican'
group by votes.voter_id
having vote_count = 1;

//atau

select voters.first_name,
voters.last_name,
voters.party
from voters
where party != 'democrat'
and party != 'republican'
and voters.id in (
select votes.voter_id
from votes
group by votes.voter_id
having count(votes.voter_id) = 1
);

delete :

delete
from voters
where party != 'democrat'
and party != 'republican'
and voters.id in (
select votes.voter_id
from votes
group by votes.voter_id
having count(votes.voter_id) = 1
);

2.

//select
select first_name, last_name, homeowner, employed, children_count, party_duration
from voters
where homeowner > 0 and employed > 0 and children_count = 0 and party_duration < 3
and voters.id in (
select votes.voter_id
from votes
join congress_members
on votes.politician_id = congress_members.id
where congress_members.grade_current > 12
);

//delete
delete
from voters
where homeowner > 0 and employed > 0 and children_count = 0 and party_duration < 3
and voters.id in (
select votes.voter_id
from votes
join congress_members
on votes.politician_id = congress_members.id
where congress_members.grade_current > 12
);


# Release 2
1.
//select
select voters.id, voters.first_name, voters.last_name, voters.age, voters.gender, voters.children_count, votes.politician_id
from voters
join votes
on voters.id = votes.voter_id
where voters.age > 80 and voters.gender = 'male' and voters.children_count = 0
group by voters.id;

//update
update votes
set politician_id = 346
where voter_id in (
select id
from voters
where age > 80 and gender = 'male' and children_count = 0
);

2.

//terpintar
select id, name, grade_current
from congress_members
order by grade_current desc limit 1;

//terendah
select id, name, grade_current
from congress_members
order by grade_current limit 1;

//cek
select congress_members.name as "Congress Name", (voters.first_name || ' ' || voters.last_name) as "Voters Name", votes.politician_id
from voters
join votes
on voters.id = votes.voter_id
join congress_members
on votes.politician_id = congress_members.id
where voters.id = votes.voter_id
and votes.politician_id in (
select id
from congress_members
group by id
order by grade_current desc limit 1
);

//update
update votes
set politician_id = (
select id
from congress_members
group by id
order by grade_current asc limit 1
)
where voter_id in (
select voters.id
from voters
join votes
on voters.id = votes.voter_id
where votes.politician_id = 530
);

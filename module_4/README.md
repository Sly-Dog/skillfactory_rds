with main as 
(SELECT f.flight_id,
        'Anapa' "departure_city",
        f.arrival_airport,
        sum(t.amount) total_amount ,
        extract(epoch from f.actual_arrival - f.actual_departure)/60 minutes_of_fly,
        f.actual_arrival,
        f.aircraft_code,
        count(t.ticket_no) sold_bp
   FROM dst_project.flights f
        join dst_project.ticket_flights t on t.flight_id = f.flight_id
  WHERE f.actual_departure  is not null 
        and f.departure_airport = 'AAQ'
        and EXTRACT(month FROM f.actual_departure) in (12,1,2)
  GROUP by f. arrival_airport,
        f. flight_id,
        f. actual_departure,
        f. actual_arrival,
        f. aircraft_code    ),

seats as    
(SELECT aircraft_code, count(seat_no) all_bp
   FROM dst_project.seats
  GROUP by aircraft_code    )

    
 SELECT m. flight_id,
        m. departure_city,
        p. city arrival_city,
        m. total_amount,
        m. sold_bp,
        s. all_bp,
        c. model,
        m. minutes_of_fly,
        p. longitude,
        p. latitude,
        m. actual_arrival
   FROM main m
        join seats                  s on s.aircraft_code = m.aircraft_code
        join dst_project.airports   p on p.airport_code  = m.arrival_airport
        join dst_project.aircrafts  c on c.aircraft_code = m.aircraft_code
  ORDER BY total_amount
